# Installation instructions for quark-1 testnet

> **Note: we assume that you've already initialized the test smart contract. You will need the contract address to put it in `config.toml` to make the relayer only work with packets coming from this contract.**

This tutorial is for connecting with cosmoshub testnet network (theta-testnet-001). To connect hermes with juno, go through the same steps (from step 2) but change the user name and service name, and uncomment the chain config for juno in `config.toml`. This tutorial installs hermes as a daemon.

## 1. Install Hermes v1.0.0

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

## 2. Add user

```
$ sudo useradd -m ibc-cosmoshub-rly
$ sudo su ibc-cosmoshub-rly && cd ~/
$ mkdir ~/.hermes
```

## 3. Create a systemd unit

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

## 4. Configure the relayer

Copy the example [config](https://github.com/neutron-org/testnets/blob/main/quark/ibc-relayer/config.toml) into `~/.hermes/config.toml` **and fill the missing parameters.**

Don't forget to fill in the missing parameters (marked by TODO comments). Check that config is valid:

`$ hermes health-check`

## 5. Add keys to relayer

Don't forget to generate your mnemonics for accounts and fill in in bash commands below:

```
$ sudo su ibc-cosmoshub-rly
$ export NEUTRON_MNEMONIC="TODO"
$ export TARGET_CHAIN_MNEMONIC="TODO" # e.g. Juno or Hub
$ export TARGET_CHAIN_ID="TODO" # e.g., theta-testnet-001 for Cosmos Hub testnet
$ hermes keys add --chain quark-1 --mnemonic-file <(echo "$NEUTRON_MNEMONIC") --key-name neutron-ibc-relayer
$ hermes keys add --chain $TARGET_CHAIN_ID --mnemonic-file <(echo "$TARGET_CHAIN_MNEMONIC") --key-name cosmoshub-ibc-relayer
```

## 6. Check funds

Make sure that the relayer keys provided on previous step have enough funds. You can find top up istructions [here](https://github.com/neutron-org/testnets/blob/main/quark/testcases/ICA+ICQ.md#getting-ready).

## 7. Run the service

Start it:

`$ sudo systemctl start neutron-ibc-cosmoshub-relayer.service`

Make it run on each boot:

`$ sudo systemctl enable neutron-ibc-cosmoshub-relayer.service`

## 8. Make sure it's running okay

Service status:

`$ sudo systemctl status neutron-ibc-cosmoshub-relayer.service`

Logs:

`$ journalctl --unit=neutron-ibc-cosmoshub-relayer`

## 9. Create connection between chains

For Cosmos hub:

```bash
$ sudo su ibc-cosmoshub-rly
$ hermes create connection --a-chain quark-1 --b-chain theta-testnet-001
$ exit
```

For Juno:

```
$ sudo su ibc-juno-rly
$ hermes create connection --a-chain quark-1 --b-chain uni-5
$ exit
```

You will see a lot of output, but you are only interested in the `connection_id` on Neutron:

```
SUCCESS Connection {
    delay_period: 0ns,
    a_side: ConnectionSide {
        chain: BaseChainHandle {
            chain_id: ChainId {
                id: "neutron-devnet-1",
                version: 1,
            },
            runtime_sender: Sender { .. },
        },
        client_id: ClientId(
            "07-tendermint-7",
        ),
        connection_id: Some(
            ConnectionId(
                "connection-7", <<< THIS CONNTECTION_ID
            ),
        ),
    },
```

<aside>
💡 Save the newly created neutron `connection_id` somewhere — is is required to run the testing script.
</aside>

