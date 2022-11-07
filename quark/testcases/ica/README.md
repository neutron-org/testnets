# Validators Instruction to test testnet

# Testing Quark testnet

## Upload testing script
- Upload the [script](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/validator_test.sh) to validator machine 

## Upload contracts

- At first get artifacts from [https://github.com/neutron-org/neutron-contracts/tree/neutron_audit_oak_19_09_2022_fixes/artifacts](https://github.com/neutron-org/neutron-contracts/tree/neutron_audit_oak_19_09_2022_fixes/artifacts)
- Upload them to your node
- Instantiate contract (store the address of contract for hermes config later)
    
    <aside>
    💡 Store the address of the contract somewhere. If will be needed for IBC relayer to whitelist (allow) operations only from this contract.
    
    </aside>
    

## Getting ready to test on CosmosHub

If you don't have `atom`, then you can go through the following steps:

- if you don't have Keplr [install it](https://www.keplr.app/)
- if you don't have  CosmosHub testnet in your Keplr go to this [Jsfiddle](https://jsfiddle.net/kht96uvo/1/)
- copy your CosmosHub testnet address
- go to [Faucet](https://discord.com/channels/669268347736686612/953697793476821092) channel in Discort and get your `atoms`

## Getting ready to test on Juno

You don't have `junox` you can go through the following steps:

- if you don't have Keplr [install it](https://www.keplr.app/)
- if you don't have uni (Juno's testnet) in your Keplr go to this [Jsfiddle](https://jsfiddle.net/superatik/L6bys84z/1/)
- copy your Uni address
- go to [Faucet](https://faucet.roguenet.io/) and get your `junox`

## Prepare IBC relayer (Hermes)

[Install Hermes for quark-1](https://github.com/neutron-org/testnets/blob/main/quark/ibc-relayer/instruction.md)

## Create connection between chains

```bash
$ sudo su ibc-cosmoshub-rly
$ hermes create connection --a-chain quark-1 --b-chain theta-testnet-001
$ exit

$ sudo su ibc-juno-rly
$ hermes create connection --a-chain quark-1 --b-chain uni-5
$ exit
```

<aside>
💡 Write newly created neutron connection id’s somewhere - they are needed for running scripts

</aside>

## Test

- Upload [testing script](https://github.com/neutron-org/neutron-contracts/blob/neutron_audit_oak_19_09_2022_fixes/validator_test.sh) to your node
- run this script on your server `./validator_test.sh /path/to/contracts/neutron_validators_test.wasm YOUR_CONNECTION_ID`
- follow the script instructions
- in case of error contact dev team via [Discord](https://discord.com/channels/986573321023942708/1030044052529352724) or [Telegram](https://t.me/neutron_community)