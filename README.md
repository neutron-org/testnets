# Neutron Testnets

This repository contains information about Neutron testnets.

## Quark Testnet

## RPC nodes

| Validator | RPC endpoint                | GRPC endpoint                | REST endpoint                |
|-----------|-----------------------------|------------------------------|------------------------------|
| Neutron   | https://rpc.quark.ntrn.info | https://grpc.quark.ntrn.info | https://rest.quark.ntrn.info |

## Snapshots service

All snapshots for Quark-1 tesnet are available at: https://snapshot.quark.ntrn.info
Use `wget -O - https://snapshot.quark.ntrn.info/<snapshot name> | lz4 -d | tar -xvf -` command in order to download and unpack snapshot.


### IBC Relayers

**Note:** we can not execute interchain transactions on Osmosis testnet yet.

| Target chain                  | Connection ID |
|-------------------------------|---------------|
|                               |               |


### ICQ Relayers

| Target chain          | Connection ID |
|-----------------------|---------------|
|                       |               |


### Faucet

http://faucet.quark.ntrn.info


### Block explorer

http://explorer.quark.ntrn.info)


### Documentation & tutorials

1. https://docs.neutron.org/
2. [IBC relayer configuration](./ibc-relayer/instruction.md)
3. [Neutron Query Relayer](./icq-relayer/README.md)
4. [GenTxs submission](./quark/README.md)
5. [Running quark testnet node](./quark/RUNNING.md)
6. [ICA+ICQ tasks](./testcases/ICA%2BICQ.md)
7. [Additional tasks](./testcases/Additional%20tasks.md)
