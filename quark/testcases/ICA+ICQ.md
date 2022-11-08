# Quark Testnet Validator Instructions (ICA, ICQ)

## Overview

This document contains the description of the ICA and ICQ test cases for the Quark testnet: ICA test cases are about executing an bunch of interchain transactions, and ICQ test cases are about registering a bunch of interchain queries & making sure that responses to those queries were submitted. Both ICA and ICQ test cases require deploying a smart contract on the Neutron chain, running a relayer (IBC and ICQ respectively) and interacting with the deployed contracts.

To make everyone's life easier, Neutron team prepared a special [smart contract](https://github.com/neutron-org/neutron-contracts/tree/neutron_audit_oak_19_09_2022_fixes/contracts/neutron_validator_test) for the test cases, as well as a testing [script](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/validator_test.sh) that goes through all the steps described in the test cases. This means that, ultimately, all you need to do is:

1. Set up your node,
2. Set up two relayers (IBC and ICQ, see the instructions below),
3. Follow (or simply run) the testing script,
4. Collect the output and paste in to the results [submission form](TODO).

## Getting ready

The testing script does not set up the IBC and ICQ relayers. You will need to set up both the IBC and the ICQ relayers prior to executing the script. In order to do that, you need to have some testnet tokens both on Neutron and on the target chain of your choice (we have instructions for Cosmos hub and Juno).

### Getting testnet tokens on target chain

#### Cosmos hub

If you don't have `atom`s, then you can go through the following steps:

- If you don't have Keplr [install it](https://www.keplr.app/),
- If you don't have  CosmosHub testnet in your Keplr, go to this [Jsfiddle](https://jsfiddle.net/kht96uvo/1/),
- Copy your CosmosHub testnet address
- Go to [Faucet](https://discord.com/channels/669268347736686612/953697793476821092) channel in Discord and get your `atoms`.

#### Juno

You don't have `junox` you can go through the following steps:

- If you don't have Keplr, [install it](https://www.keplr.app/),
- If you don't have uni (Juno's testnet) in your Keplr, go to this [Jsfiddle](https://jsfiddle.net/superatik/L6bys84z/1/),
- Copy your Uni address,
- Go to [Faucet](https://faucet.roguenet.io/) and get your `junox`.

### Getting testnet tokens on Neutron

This is done as one of the [steps](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/validator_test.sh#L85) in the testing script.

### Setting up the relayers

#### IBC relayer

See the [instruction](https://github.com/neutron-org/testnets/blob/main/quark/ibc-relayer/instruction.md).

> Note: the IBC rlayer allows you to [specify](https://github.com/neutron-org/testnets/blob/9146e8c3f8d954798d478a822592888d7ff52e66/quark/ibc-relayer/config.toml#L164) the channels that the relayer will work with. You will want to configure your relayer to only process the interchain transactions submitted by your test contract. This can only be done after you have deployed your test contract (because you need to know the contract address); you can restart the relayer with updated configuration to make these settings effective. See the [documentation](https://docs.neutron.org/neutron/interchain-txs/overview#relaying) for more information.

2. ICQ relayer setup [instruction](TODO).

> Note: the ICQ rlayer allows you to [specify](TODO) the contract addresses that the relayer will work with. You will want to configure your relayer to only process the interchain queries submitted by your test contract. This can only be done after you have deployed your test contract (because you need to know the contract address); you can restart the relayer with updated configuration to make these settings effective. See the [documentation](https://docs.neutron.org/relaying/icq-relayer#relayer-application-settings) for more information.

#### Create connection between chains

```bash
$ sudo su ibc-cosmoshub-rly
$ hermes create connection --a-chain quark-1 --b-chain theta-testnet-001
$ exit

$ sudo su ibc-juno-rly
$ hermes create connection --a-chain quark-1 --b-chain uni-5
$ exit
```

<aside>
💡 Save the newly created neutron `connection_id`s somewhere — they are required to run the testing script.
</aside>

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

1. Upload the testing contract [artifact]([TODO](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/artifacts/neutron_validators_test.wasm)),
2. Register a tx query (send a [message](TODO) to the contract), and share tx hash,
3. Register a kv query (send a [message](TODO) to the contract), and share tx hash,
4. Wait until the relayer submits the responses, share the txs in a google form,
5. Control contract address balance during query registration to register balance reduction for deposit (please read the deposits [documentation](https://docs.neutron.org/neutron/interchain-queries/overview#query-creation-deposit))
6. Delete **the tx query** by sending a [message](TODO) to the contract before the query submit timeout event, to collect the deposit to contract address,
7. Delete **the kv query** using a 3rd party address (not the contract address) after query submit timeout event, to collect deposit to this, 3rd party address.

## Running the tasks


## Test

1. Upload the [testing script](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/validator_test.sh) to your node,
2. Run this script on your server `./validator_test.sh /path/to/contracts/neutron_validators_test.wasm YOUR_CONNECTION_ID`,
3. Follow the script instructions,
4. In case of errors contact the dev team on [Discord](https://discord.com/channels/986573321023942708/1030044052529352724) or [Telegram](https://t.me/neutron_community).

