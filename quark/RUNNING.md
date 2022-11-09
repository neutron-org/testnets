# Neutron Testnet quark-1

**Genesis File**


```bash
curl -s https://raw.githubusercontent.com/neutron-org/testnets/main/quark/genesis.json > ~/.neutrond/config/genesis.json
```

**Genesis sha256**

```bash
sha256sum ~/.neutrond/config/genesis.json
# 357c4d33fad26c001d086c0705793768ef32c884a6ba4aa73237ab03dd0cc2b4
```

**get testnet version of neutron**

```
$ git clone -b v0.1.0 https://github.com/neutron-org/neutron.git
$ cd neutron
$ make install
```

**neutrond version**

```bash
$ neutrond version --long
name: neutron
server_name: neutrond
version: 0.1.0
commit: a9e8ba5ebb9230bec97a4f2826d75a4e0e6130d9

```

**Seed nodes**

```
e2c07e8e6e808fb36cca0fc580e31216772841df@seed-1.quark.ntrn.info:26656,c89b8316f006075ad6ae37349220dd56796b92fa@tenderseed.ccvalidators.com:29001
```

**Persistent Peers**

```
fcde59cbba742b86de260730d54daa60467c91a5@23.109.158.180:26656,5bdc67a5d5219aeda3c743e04fdcd72dcb150ba3@65.109.31.114:2480,3e9656706c94ae8b11596e53656c80cf092abe5d@65.21.250.197:46656,9cb73281f6774e42176905e548c134fc45bbe579@162.55.134.54:26656,27b07238cf2ea76acabd5d84d396d447d72aa01b@65.109.54.15:51656,f10c2cb08f82225a7ef2367709e8ac427d61d1b5@57.128.144.247:26656,20b4f9207cdc9d0310399f848f057621f7251846@222.106.187.13:40006,5019864f233cee00f3a6974d9ccaac65caa83807@162.19.31.150:55256,2144ce0e9e08b2a30c132fbde52101b753df788d@194.163.168.99:26656,b37326e3acd60d4e0ea2e3223d00633605fb4f79@nebula.p2p.org:26656
```

You can find more peers in the [peers](./peers/) directory.


## Running in production

**Consider using [Cosmovisor](https://github.com/cosmos/cosmos-sdk/tree/main/tools/cosmovisor) to make your life easier.**

Download Genesis file. Put it in your `/home/<user>/.neutrond/config` folder.

If you have not installed cosmovisor, create a systemd file for your Neutrond service:

```sh
sudo nano /etc/systemd/system/neutrond.service
```

Update your `/home/<user>/.neutrond/config/config.toml` and set `timeout_commit = "2s"`. Also for better network security it is recommended to set `minimum-gas-prices = "0.001untrn"` in the `/home/<user>/.neutrond/config/app.toml`

Copy and paste the following and update `<YOUR_USERNAME>`:

```sh
Description=Neutrond daemon
After=network-online.target

[Service]
User=juno
ExecStart=/home/<YOUR_USERNAME>/go/bin/neutrond start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
```

Enable and start the new service:

```sh
sudo systemctl enable neutrond
sudo systemctl start neutrond
```

Check status:

```sh
neutrond status
```

Check logs:

```sh
journalctl -u neutrond -f
```


## Create Testnet Validator
This section applies to those who are looking to join the testnet post genesis.

1. Create a local key pair in the Keyring

   ```shell
   $ neutrond keys add <key-name>
   $ neutrond keys show <key-name> -a
   ```

2. Request tokens from faucet: http://faucet.quark.ntrn.info

3. Create validator

   ```shell
   $ neutrond tx staking create-validator \
   --amount 1000000untrn \
   --chain-id=quark-1 \
   --pubkey=$(neutrond tendermint show-validator) \
   --moniker="<moniker-name>" \
   --website=<your-node-website> \
   --details=<your-node-details> \
   --commission-rate="0.10" \
   --commission-max-rate="0.20" \
   --commission-max-change-rate="0.01" \
   --min-self-delegation="1" \
   --from <key-name> 
   ```




