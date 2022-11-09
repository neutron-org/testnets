# Quark Testnet Validator Instructions (ICA, ICQ)

## Overview

This document contains the description of the ICA and ICQ test cases for the Quark testnet: ICA test cases are about executing an bunch of interchain transactions, and ICQ test cases are about registering a bunch of interchain queries & making sure that responses to those queries were submitted. Both ICA and ICQ test cases require deploying a smart contract on the Neutron chain, running a relayer (IBC and ICQ respectively) and interacting with the deployed contracts.

To make everyone's life easier, Neutron team prepared a special [smart contract](https://github.com/neutron-org/neutron-contracts/tree/neutron_audit_oak_19_09_2022_fixes/contracts/neutron_validator_test) for the test cases, as well as a couple of testing testing scripts ([1](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/validator_test_upload_contract.sh), [2](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/validator_test.sh)) that help you to go through all the steps described in the test cases. This means that, ultimately, all you need to do is:

1. Set up your node,
2. Upload the test contract,
3. Set up two relayers (IBC and ICQ, see the instructions below),
4. Run the testing script,
5. Collect the output and paste in to the results [submission form](TODO).

## Getting ready

The testing script does not set up the IBC and ICQ relayers. You will need to set up both the IBC and the ICQ relayers prior to executing the script. In order to do that, you need to have some testnet tokens both on Neutron and on the target chain of your choice (we have instructions for Cosmos hub and Juno).

### Get the `neutrond` binary

See the [instructions](https://github.com/neutron-org/testnets/blob/main/quark/README.md#node-installation), or download a binary from the [releases](https://github.com/neutron-org/neutron/releases/tag/v0.1.0) page.

### Getting testnet tokens on target chain

Testnet tokens are required for the operation of the IBC relayer. **You will specify the address used in this section in `hermes` configuration as the address on the target chain of your choice.**

> NOTE: https://jsfiddle.net might not be accessible from your location without VPN.

#### Cosmos hub

If you don't have `atom`s, then you can go through the following steps:

- If you don't have Keplr [install it](https://www.keplr.app/),
- If you don't have  CosmosHub testnet in your Keplr, go to this [Jsfiddle](https://jsfiddle.net/kht96uvo/1/), a `theta-testnet-001` network will be now available in Keplr,
- Copy your CosmosHub testnet address (or generate a new one),
- Go to [Faucet](https://discord.com/channels/669268347736686612/953697793476821092) channel in Discord and get your `atom`s (make sure that you are added to the sever first: https://discord.gg/cosmosnetwork).

#### Juno

You don't have `junox` you can go through the following steps:

- If you don't have Keplr, [install it](https://www.keplr.app/),
- If you don't have uni (Juno's testnet) in your Keplr, go to this [Jsfiddle](https://jsfiddle.net/superatik/L6bys84z/1/),
- Copy your Uni address (or generate a new one),
- Go to [Faucet](https://faucet.roguenet.io/) and get your `junox`.

### Generate the relayer address on Neutron and get testnet `$ntrn` tokens

Testnet tokens are required for the operation of the IBC relayer. **You will specify the address used in this section in `hermes` configuration as the address on the Neutron chain.**

1. Go to the Faucet (http://23.109.159.28/) and get tokens for your relayer address that is going to be used on the Neutron chain,
2. Make sure tx is passed by going to http://23.109.159.28:3333/accounts/<your_relayer_address>.


### Uploading the test contract

You need to know the address of the test contract in order to configure ICA and ICQ relayers properly (so that they process only the messages related to that specific contract). In order to do that:

1. Upload the contract instantiation [script](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/validator_test_upload_contract.sh) to your machine,
2. Upload the test contract [artifact](https://github.com/neutron-org/neutron-contracts/raw/neutron_audit_oak_19_09_2022_fixes/artifacts/neutron_validators_test.wasm) to your machine.

After the script and the artifact are uploaded, execute the script (`NODE_URL` configures the node address; don't forget about the `tcp://` prefix!):

```
$ NODE_URL=tcp://23.109.158.236:26657 bash validator_test_upload_contract.sh neutron_validators_test.wasm
Node url: tcp://23.109.158.236:26657
Chain id: neutron-devnet-1
Enter keyring passphrase: # here you enter the new password for your freshly created keyring
Re-enter keyring passphrase:
Local address in neutron: neutron1mljg6r7r2wzwpvm5fv************* # This is the address that was generated for you
Key mnemonic: future kite hurry duck chat absorb curtain error render conduct tone model void start flag chronic brother comic spoil trim idle false siren nephew
Key name: validator_test

Please go to http://23.109.159.28/ and get tokens for neutron1mljg6r7r2wzwpvm5fvcgt80nn9kevv2gp4sy2v # Here you are prompted to visit the Faucet address and get some testnet $ntrn tokens
Make sure tx is passed by going to http://23.109.159.28:3333//accounts/neutron1mljg6r7r2wzwpvm5fvcgt80nn9kevv2gp4sy2v
Hit enter when ready

Upload the queries contract
Enter keyring passphrase:
Contract code id: 10

Instantiate the contract
Enter keyring passphrase:
gas estimate: 195479
Contract address: neutron1hzz0s0ucrhdp6tue2lxk3c03nj6f60qy463we7lgx0wudd72ctmsw8xeng
```
This script:
* Creates an address for you,
* Prompts you to get testnet `$ntrn` tokens from the Faucet,
* Uploads the contract code,
* Instantiates the contract.

The contract address, in this case `neutron1hzz0s0ucrhdp6tue2lxk3c03nj6f60qy463we7lgx0wudd72ctmsw8xeng`, is saved to `./contract_address.tmp`.

### Setting up the relayers

#### IBC relayer

See the [instruction](https://github.com/neutron-org/testnets/blob/main/quark/ibc-relayer/instruction.md).

> Note: you should first start the relayer **without** specifying [the channel](https://github.com/neutron-org/testnets/blob/main/quark/ibc-relayer/config.toml#L163-L165) that the relayer will work with using the contract address from the previous step. You can add this config and restart the relayer **after** running the [testing sript](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/validator_test.sh) (see below) to make `hermes` only relay packets that are produced by your test contract. See the [documentation](https://docs.neutron.org/neutron/interchain-txs/overview#relaying) for more information.

> Note: you will need the `connection_id` on Neutron side from this step later.

#### ICQ relayer setup 

See the [instruction](https://github.com/neutron-org/testnets/blob/main/quark/icq-relayer/README.md).

> Note: don't forget to [specify](TODO) the contract addresses that the relayer will work with using the contract address from the previous step. See the [documentation](https://docs.neutron.org/relaying/icq-relayer#relayer-application-settings) for more information.

## Test cases [informational]

This section contains the desciption of the ICA and ICQ test cases. The *single* testing [script](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/validator_test.sh) goes through all the steps in both the ICA and ICQ test cases, which you can check by reading the script. This section simply provides you with the description of the tasks.

### ICA

1. Upload the testing contract [artifact](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/artifacts/neutron_validators_test.wasm),
2. Execute an interchain transaction (send a [message](TODO) to the contract) that should return a successful ACK, share the tx links,
3. Execute an interchain transaction (send a [message](TODO) to the contract) that should return an error ACK, share the tx links,
4. Execute an interchain transaction (send a [message](TODO) to the contract) that should return an successful ACK that will be processed by the contract with an error, share the tx links.

> Note: when the ICA module executes an interchain transaction on the host chain, an IBC acknowledgement packet gets sent to the controller chain. This acknowledgement can either be a successful acknowledgement or an error acknowledgement. 

> Note: when we say "share the tx links", we have two transactions in mind. The first one is executed by you, from your address, and sends a message to the test contract which triggers the interchain transaction execution. The second one is executed by your IBC relayer, and submits the IBC acknowledgement packet on Neutron. You can read more about Interchain Accounts module [here](https://ibc.cosmos.network/main/apps/interchain-accounts/overview.html), and you can read about Neutron's Interchain Transactions module (which uses the ICA module) [here](https://docs.neutron.org/neutron/interchain-txs/overview).

### ICQ

1. Upload the testing contract [artifact](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/artifacts/neutron_validators_test.wasm),
2. Register a tx query (send a [message](TODO) to the contract), and share tx hash,
3. Register a kv query (send a [message](TODO) to the contract), and share tx hash,
4. Wait until the relayer submits the responses, share the txs in a google form,
5. Control contract address balance during query registration to register balance reduction for deposit (please read the deposits [documentation](https://docs.neutron.org/neutron/interchain-queries/overview#query-creation-deposit)),
6. Delete **the tx query** (send a [message](TODO) to the contract) before the query submit timeout event, to collect the deposit to contract address,
7. Delete **the kv query** using a 3rd party address (not the contract address) after query submit timeout event (send a [message](TODO) to the Interchain Queries module), to collect deposit to this, 3rd party address.

## Running the tasks

## Test

1. Upload the [testing script](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/validator_test.sh) to your node,
2. Run this script on your server: `bash validator_test.sh YOUR_CONNECTION_ID`, where `YOUR_CONNECTION_ID` is the connection identifier (Neutron side) that you saved after running the IBC relayer,
3. Follow the script instructions,
4. Collect the output and paste in to the results [submission form](TODO).

> NOTE: when prompted for a passphrase, use the same passphrase you used when uploading the test contract.

> NOTE: if you see the message `Please send 0.02 atom to cosmosXXXXXXXXXXXX`, use the Keplr wallet to send the required amount to that address. You can check the delivery using a block explorer, e.g., https://explorer.theta-testnet.polypore.xyz/accounts/cosmos1c5gl8epk99jvz23dhnyp5et9rmm8wdq89v9hqnvt0pr74j5d2s7sjjxlp7

In case of errors, contact the dev team on [Discord](https://discord.com/channels/986573321023942708/1030044052529352724) or [Telegram](https://t.me/neutron_community).

