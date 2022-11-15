# Quark Testnet Validator Instructions (ICA, ICQ)

## Overview

This document contains the description of the ICA and ICQ test cases for the Quark testnet: ICA test cases are about executing a bunch of interchain transactions, and ICQ test cases are about registering a bunch of interchain queries & making sure that responses to those queries were submitted. Both ICA and ICQ test cases require deploying a smart contract on the Neutron chain, running a relayer (IBC and ICQ respectively) and interacting with the deployed contracts.

Note

To make everyone's life easier, Neutron team prepared a special [smart contract](https://github.com/neutron-org/neutron-contracts/tree/neutron_audit_oak_19_09_2022_fixes/contracts/neutron_validator_test) for the test cases, as well as a couple of testing scripts ([1](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/validator_test_upload_contract.sh), [2](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/validator_test.sh)) that help you to go through all the steps described in the test cases. This means that, ultimately, all you need to do is:

1. [Set up your node](#get-the-neutrond-binary);
2. [Upload the test contract](#uploading-the-test-contract);
3. [Set up two relayers](#setting-up-the-relayers);
4. [Run the testing script](#running-the-tasks);
5. Collect the output and paste in to the results [submission form](https://forms.gle/cyEdWfFTygkvcLEQ7).

## Getting ready

None of the testing scripts sets up the IBC and ICQ relayers. You will need to set up both the IBC and the ICQ relayers prior to executing the scripts. In order to do that, you need to have some testnet tokens both on Neutron and on the target chain of your choice (we have instructions for Cosmos hub and Juno).

### Get the `neutrond` binary

See the [instructions](https://github.com/neutron-org/testnets/blob/main/quark/README.md#node-installation), or download a binary from the [releases](https://github.com/neutron-org/neutron/releases/latest) page.

### Generate the relayers' address on Neutron and get testnet `$ntrn` tokens

Neutron testnet tokens are required for the operation of the IBC and ICQ relayers. **You will specify the addresses used in this section in [Hermes](#ibc-relayer) and [ICQ Relayer](#icq-relayer-setup) configuration as the address on the Neutron chain.**

1. Generate keys with following commands:

`neutrond keys add ibc-relayer`

`neutrond keys add icq-relayer`

and save the mnemonics;

2. Go to the [Faucet](http://faucet.quark.ntrn.info/) and get tokens for the addresses you generated on previous step;
3. Make sure tx is passed at the Neutron testnet explorer: http://explorer.quark.ntrn.info/accounts/<your_relayer_address>.

### Getting testnet tokens on target chain

Testnet tokens are required for the operation of the IBC relayer. **You will specify the address used in this section in [Hermes](#ibc-relayer) configuration as the address on the target chain of your choice.**

> NOTE: the following guides on getting testnet tokens contain visiting the https://jsfiddle.net/. This resource might not be accessible from your location without VPN.

#### Cosmos hub

If you don't have `atom`s, then you can go through the following steps:

- If you don't have Keplr [install it](https://www.keplr.app/);
- If you don't have CosmosHub testnet in your Keplr, follow to the [Jsfiddle](https://jsfiddle.net/kht96uvo/1/), and a `theta-testnet-001` network will become available in Keplr;
- For simplicity, you can re-use the mnemonic of `ibc-relayer` key (Keplr -> Add Account -> Import existing account);
- Go to [Faucet](https://discord.com/channels/669268347736686612/953697793476821092) channel in Discord and get your `atom`s (make sure that you are added to the sever first: https://discord.gg/cosmosnetwork).

#### Juno

You don't have `junox` you can go through the following steps:

- If you don't have Keplr, [install it](https://www.keplr.app/);
- If you don't have Juno testnet in your Keplr, follow to the [Jsfiddle](https://jsfiddle.net/superatik/L6bys84z/1/), and a `uni-5` network will become available in Keplr;
- For simplicity, you can re-use the mnemonic of `ibc-relayer` key (Keplr -> Add Account -> Import existing account);
- Go to [Faucet](https://faucet.roguenet.io/) and get your `junox`.

### Uploading the test contract

You need to know the address of the test contract in order to configure ICA and ICQ relayers properly (so that they process only the messages related to that specific contract). In order to do that:

1. Upload the contract instantiation [script](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/validator_test_upload_contract.sh) to your machine;
2. Upload the test contract [artifact](https://github.com/neutron-org/neutron-contracts/raw/neutron_audit_oak_19_09_2022_fixes/artifacts/neutron_validators_test.wasm) to your machine.

After the script and the artifact are uploaded, execute the script (`NODE_URL` configures the node address; don't forget about the `tcp://` prefix!):

```
$ NODE_URL=tcp://<your_node_host:port> bash validator_test_upload_contract.sh neutron_validators_test.wasm
Node url: tcp://<your_node_host:port>
Chain id: quark-1
Enter keyring passphrase: # here you enter the new password for your freshly created keyring
Re-enter keyring passphrase:
Local address in neutron: <neutron_address> # This is the address that was generated for you
Key mnemonic: <mnemonic>

Please go to http://faucet.quark.ntrn.info/ and get tokens for <neutron_address> # Here you are prompted to visit the Faucet address and get some testnet $ntrn tokens
Make sure tx is passed by going to http://explorer.quark.ntrn.info/accounts/<neutron_address>
Hit enter when ready

Upload the queries contract
Enter keyring passphrase:
Contract code id: 10

Instantiate the contract
Enter keyring passphrase:
gas estimate: 195479
Contract address: <test_contract_address>
```
This script:
* Creates a tmp address for you;
* Prompts you to get testnet `$ntrn` tokens from the Faucet;
* Uploads the contract code on behalf of the tmp address;
* Instantiates the contract.

The <test_contract_address> is saved to `./contract_address.tmp` just in case.

### Setting up the relayers

#### IBC relayer

See the [instruction](https://github.com/neutron-org/testnets/blob/main/quark/ibc-relayer/instruction.md).

> Note: you should first start the relayer **without** specifying [the channel](https://github.com/neutron-org/testnets/blob/main/quark/ibc-relayer/config.toml#L163-L165) that the relayer will work with using the contract address from the previous step. You can add this config and restart the relayer **after** running the [testing script](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/validator_test.sh) (see below) to make `hermes` only relay packets that are produced by your test contract. See the [documentation](https://docs.neutron.org/neutron/interchain-txs/overview#relaying) for more information.

> Note: you will need the `connection_id` on Neutron (`a_side`) from this step later.

> Note: you will need the mnemonic of `ibc-relayer` key from the [keys generation](#generate-the-relayers-address-on-neutron-and-get-testnet-ntrn-tokens) step both for `NEUTRON_MNEMONIC` and `TARGET_CHAIN_MNEMONIC` parameters.

#### ICQ relayer setup 

See the [instruction](https://github.com/neutron-org/testnets/blob/main/quark/icq-relayer/README.md).

> Note: don't forget to specify the contract address that the relayer will work with using the contract address from the [previous step](#uploading-the-test-contract). See the [documentation](https://docs.neutron.org/relaying/icq-relayer#relayer-application-settings) for more information. The configuration option you are looking for is `RELAYER_REGISTRY_ADDRESSES`.

> Note: you will need the mnemonic of `icq-relayer` key from the [keys generation](#generate-the-relayers-address-on-neutron-and-get-testnet-ntrn-tokens) step.

## Test cases (informational)

This section contains the desciption of the ICA and ICQ test cases. The *single* testing [script](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/validator_test.sh) goes through all the steps in both the ICA and ICQ test cases, which you can check by reading the script. 

> **Note: this section simply provides you with the description of the tasks, no actions are required here. You will go through all the steps in the next section by running the test script.**

### ICA

1. Upload the testing contract [artifact](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/artifacts/neutron_validators_test.wasm),
2. Execute an interchain transaction (send a [message](https://github.com/neutron-org/neutron-contracts/blob/0ba9a36c6d26166cc7051436ec21417031de1334/contracts/neutron_validator_test/src/msg.rs#L39-L45) to the contract) that should return a successful ACK, share the tx links,
3. Execute an interchain transaction (send a [message](https://github.com/neutron-org/neutron-contracts/blob/0ba9a36c6d26166cc7051436ec21417031de1334/contracts/neutron_validator_test/src/msg.rs#L39-L45) to the contract) that should return an error ACK, share the tx links,
4. Execute an interchain transaction (send a [message](https://github.com/neutron-org/neutron-contracts/blob/0ba9a36c6d26166cc7051436ec21417031de1334/contracts/neutron_validator_test/src/msg.rs#L39-L45) to the contract) that should return a successful ACK that will be processed by the contract with an error, share the tx links.

> Note: when the ICA module executes an interchain transaction on the host chain, an IBC acknowledgement packet gets sent to the controller chain. This acknowledgement can either be a successful acknowledgement or an error acknowledgement. 

### ICQ

1. Upload the testing contract [artifact](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/artifacts/neutron_validators_test.wasm),
2. Register a tx query (send a [message](https://github.com/neutron-org/neutron-contracts/blob/0ba9a36c6d26166cc7051436ec21417031de1334/contracts/neutron_validator_test/src/msg.rs#L66-L71) to the contract), and share tx hash,
3. Register a kv query (send a [message](https://github.com/neutron-org/neutron-contracts/blob/0ba9a36c6d26166cc7051436ec21417031de1334/contracts/neutron_validator_test/src/msg.rs#L60-L65) to the contract), and share tx hash,
4. Wait until the relayer submits the responses, share the txs in a Google form,
5. Control contract address balance during query registration to register balance reduction for deposit (please read the [documentation](https://docs.neutron.org/neutron/interchain-queries/overview#query-creation-deposit) on deposits),
6. Delete **the tx query** (send a [message](https://github.com/neutron-org/neutron-contracts/blob/0ba9a36c6d26166cc7051436ec21417031de1334/contracts/neutron_validator_test/src/msg.rs#L72-L74) to the contract) before the query submit timeout event, to collect the deposit to contract address.

## Running the tasks

### Test

1. Upload the [testing script](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/validator_test.sh) to your node,
2. Run this script on your server (`YOUR_CONNECTION_ID` is the connection identifier (Neutron side) that you saved after running the IBC relayer): 
 
`NODE_URL=tcp://<your_node_host:port> bash validator_test.sh YOUR_CONNECTION_ID`


3. Follow the script instructions,
4. Collect the output and paste in to the results [submission form](https://forms.gle/cyEdWfFTygkvcLEQ7),
5. **PLEASE DON'T FORGET TO SAVE ALL SCRIPT OUTPUT IN A SEPARATE FILE; IT MIGHT BE REQUIRED FOR DEBUGGING.**

> NOTE: when prompted for a passphrase, use the same passphrase you used when uploading the test contract.

> NOTE: if you see the message `Please send 0.02 atom to cosmosXXXXXXXXXXXX`, use the Keplr wallet to send the required amount to that address. You can check the delivery using a block explorer, e.g., https://explorer.theta-testnet.polypore.xyz/accounts/cosmos1c5gl8epk99jvz23dhnyp5et9rmm8wdq89v9hqnvt0pr74j5d2s7sjjxlp7

In case of errors, contact the dev team on [Discord](https://discord.com/channels/986573321023942708/1030044052529352724) or [Telegram](https://t.me/neutron_community).

