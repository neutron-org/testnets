# Neutron Testnets

This repository contains information about Neutron testnets.

## Quark Testnet

> **NOTE: some of the links (faucet, explorer, etc.) may not work yet as we are still setting up the services. Please stay tuned.**

### RPC nodes

| Validator | RPC endpoint                        | GRPC endpoint                        | REST endpoint                       |
|-----------|-------------------------------------|--------------------------------------|-------------------------------------|
| Neutron   | https://rpc.quark.ntrn.info         | https://grpc.quark.ntrn.info         | https://rest.quark.ntrn.info        |
| NodeStake | https://rpc-t.neutron.nodestake.top | https://grpc-t.neutron.nodestake.top | https://api-t.neutron.nodestake.top |
| Nodejumper| https://neutron-testnet.nodejumper.io:443 | https://neutron-testnet.nodejumper.io:9090 | https://neutron-testnet.nodejumper.io:1317 |
| Cabinet42 | https://neutron-rpc.42cabi.net      | https://neutron-grpc.42cabi.net      | https://neutron-api.42cabi.net      |
| SECARD    | https://neutron-testnet.secardnode.com:26657 | https://neutron-testnet.secardnode.com:899 | https://neutron-testnet.secardnode.com:1317 |
| MMS       | https://rpc-testnet-quark-1.mms.team| https://grpc-testnet-quark-1.mms.team| https://api-testnet-quark-1.mms.team|

### Snapshots service

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

http://explorer.quark.ntrn.info

https://explorer.nodestake.top/neutron-testnet

https://explorer.secardnode.com/neutron

https://t-explorer.mms.team/neutron


### Documentation & tutorials

1. https://docs.neutron.org/
2. [IBC relayer configuration](https://github.com/neutron-org/testnets/blob/main/quark/ibc-relayer/instruction.md)
3. [Neutron Query Relayer](https://github.com/neutron-org/testnets/blob/main/quark/icq-relayer/README.md)
4. [GenTxs submission](https://github.com/neutron-org/testnets/blob/main/quark/README.md)
5. [Running quark testnet node](https://github.com/neutron-org/testnets/blob/main/quark/README.md)
6. [ICA+ICQ tasks](https://github.com/neutron-org/testnets/blob/main/quark/testcases/ICA%2BICQ.md)
7. [Additional tasks](https://github.com/neutron-org/testnets/blob/main/quark/testcases/Additional%20tasks.md)
