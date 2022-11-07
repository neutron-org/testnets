# Configuration for Neutron Query Relayer

## Prerequisities

- Go programming language, version 1.18.7 or newer.

## Install relayer

You can get source code [here](https://github.com/neutron-org/neutron-query-relayer).

Run `make install` to install relayer into your `$GOHOME/bin`.
Make sure your `$GOHOME/bin` is in the `$PATH`.

## Install neutron

You can get source code [here](https://github.com/neutron-org/neutron).

Run `make install` to install relayer into your `$GOHOME/bin`.
Make sure your `$GOHOME/bin` is in the `$PATH`.

## Configuration

Run `neutrond keys add my_wallet --recover --home /path/to/home --keyring-backend test`.
Input your mnemonic. Address associated with your mnemonic should have adequate amount
of tokens to be able to submit query results (it is wise to have at least 5 neutrons).
Use [faucet](http://23.109.159.28/).

Open `.env` in your text editor and fill in following variables:
- RELAYER_NEUTRON_CHAIN_RPC_ADDR
- RELAYER_NEUTRON_CHAIN_REST_ADDR
- RELAYER_NEUTRON_CHAIN_HOME_DIR
- RELAYER_NEUTRON_CHAIN_CONNECTION_ID
- RELAYER_NEUTRON_CHAIN_CLIENT_ID
- RELAYER_TARGET_CHAIN_RPC_ADDR
- RELAYER_TARGET_CHAIN_CLIENT_ID
- RELAYER_REGISTRY_ADDRESSES
- RELAYER_STORAGE_PATH

## Run

Before starting `neutron-query-relayer`, execute `export $(grep -v '^#' .env | xargs)`
