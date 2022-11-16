# Configuration for Neutron Query Relayer

## Prerequisities

- Go programming language, version 1.18.7 or newer.

## Install relayer

> Note: if you don't want to build the relayer, you can get the binaries from the [releases](https://github.com/neutron-org/neutron-query-relayer/releases/tag/v0.1.1) page.

```
$ git clone -b v0.1.1 https://github.com/neutron-org/neutron-query-relayer.git
$ cd neutron-query-relayer
$ make install
```

This installs the relayer binary into your `$GOPATH/bin`. Make sure your `$GOPATH/bin` is in the `$PATH`. Check the version by running:

```
$ neutron_query_relayer version
Version: 0.1.1
Commit: 166b2d573b1ec6ea538e158624a1330a7d8eeb93
```

## Install neutron

See the [instructions](https://github.com/neutron-org/testnets/blob/main/quark/README.md#node-installation), or download a binary from the [releases](https://github.com/neutron-org/neutron/releases/tag/v0.1.1) page.


## Configuration

ICQ relayer must have an address on the Neutron chain, and this address must have some `$ntrn` testnet tokens on it. Check the [instruction](https://github.com/neutron-org/testnets/blob/main/quark/testcases/ICA+ICQ.md#generate-the-relayer-address-on-neutron-and-get-testnet-ntrn-tokens) on how to get testnet `$ntrn` tokens.

Run `neutrond keys add my_wallet --recover --home /path/to/home --keyring-backend test` and input your mnemonic.

Upload the template `.env` file to your machine:

```
wget https://raw.githubusercontent.com/neutron-org/testnets/main/quark/icq-relayer/.env
```

Open this file in your text editor and fill in following variables (see the [documentation](https://docs.neutron.org/relaying/icq-relayer#configuration) for detailed description of the parameters):

- RELAYER_NEUTRON_CHAIN_RPC_ADDR (e.g., `http://23.109.158.236:26657` — note: don't use `tcp://` (use `http://` instead) and avoid trailing slash)
- RELAYER_NEUTRON_CHAIN_REST_ADDR (e.g., `http://23.109.158.236:1317` — note: don't use `tcp://` (use `http://` instead) and avoid trailing slash)
- RELAYER_NEUTRON_CHAIN_HOME_DIR (e.g., `/path/to/home` — a path to home directory, use the same path as one passed to neutrond with `--home` option when importing key in previous step)
- RELAYER_NEUTRON_CHAIN_CONNECTION_ID
- RELAYER_TARGET_CHAIN_RPC_ADDR (e.g., `http://164.90.146.43:26657` — note: don't use `tcp://` (use `http://` instead) and avoid trailing slash)
- RELAYER_REGISTRY_ADDRESSES
- RELAYER_STORAGE_PATH (just some path — a new directory will be automatically created)

## Run the ICQ relayer

```
export $(grep -v '^#' .env | xargs) && neutron_query_relayer start
```
