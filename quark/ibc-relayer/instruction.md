# Installation instructions for quark-1 testnet

> To execute all steps without stops I assume you've already loaded and initialized the interchain_txs.wasm contract (It will be needed in config.toml for allowing only packets from it)

> This tutorial is for connecting with cosmoshub testnet network (theta-testnet-001). To connect hermes with juno, make same steps (from step 2) but change user name, service name, uncomment chain config for juno in config.toml.

1. Install Hermes v1.0.0
```
$ PLATFORM=`uname -a | awk '{print $(NF-1)}'`
$ curl -L "https://github.com/informalsystems/ibc-rs/releases/download/v1.0.0/hermes-v1.0.0-${PLATFORM}-unknown-linux-gnu.tar.gz" > hermes.tar.gz && \
    mkdir -p $HOME/.hermes/bin && \
    tar -C ./ -vxzf hermes.tar.gz && \
    rm -f hermes.tar.gz  && \
    mv ./hermes /usr/local/bin/
```

Check that it works and version is ok (Should be `hermes 1.0.0+ed4dd8c`)

`$ hermes --version`

2. Add user

```
$ sudo useradd -m ibc-cosmoshub-rly
$ sudo su ibc-cosmoshub-rly && cd ~
$ mkdir ~/.hermes
```

3. Create systemd unit

Create file `/etc/systemd/system/neutron-ibc-cosmoshub-relayer.service` with content:

```
[Unit]
Description=Neutron IBC CosmosHub Relayer
After=network.target

[Service]
User=ibc-cosmoshub-rly
ExecStart=/usr/local/bin/hermes start

[Install]
WantedBy=multi-user.target
```

4. Copy config from https://github.com/neutron-org/testnets/blob/main/quark-1/ibc-relayer/config.toml into `~/.hermes/config.toml` AND fill missing parameters

> NOTE: Don't forget to fill missing parameters in (marked by TODO comments)

Check that config is valid

`$ hermes health-check`

5. Add keys to relayer

> NOTE: Don't forget to generate your mnemonics for accounts and fill in in bash commands below

```
$ sudo su ibc-cosmoshub-rly
$ NEUTRON_MNEMONIC = "TODO"
$ COSMOSHUB_MNEMONIC = "TODO"
$ hermes keys add --chain quark-1 --mnemonic-file <(echo "$NEUTRON_MNEMONIC") --key-name neutron-ibc-relayer
$ hermes keys add --chain theta-testnet-001 --mnemonic-file <(echo "$COSMOSHUB_MNEMONIC") --key-name cosmoshub-ibc-relayer
```

6. Add funds to relayer keys

Use faucets to add funds to keys

7. Run service

Start it

`$ sudo systemctl start neutron-ibc-cosmoshub-relayer.service`

Make it run on each boot

`$ sudo systemctl enable neutron-ibc-cosmoshub-relayer.service`

8. Make sure it's running okay

Service status

`$ sudo systemctl status neutron-ibc-cosmoshub-relayer.service`

Logs

`$ journalctl --unit=neutron-ibc-cosmoshub-relayer`
