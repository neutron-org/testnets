# Neutron Testnet quark-1

First public Neutron testnet.
  
| Attribute | Value     |
|-----------|-----------|
| Chain ID  | `quark-1` |

## Requirements

### Hardware

* 4 Cores
* 32 GB RAM
* 2x512 GB SSD

### Software Versions

| Name               | Version  |
|--------------------|----------|
| Neutron            | v0.0.1   |
| Go                 | > 1.18   |
| Rust               | > 1.63.0 |
| Hermes IBC relayer | > 1.0    |

> **PLEASE NOTE THAT WE ARE GOING TO USE v0.0.1 FOR GENTXS ONLY AND v0.1.0 (TO BE RELEASED SHORTLY) FOR THE ACTUAL TESTNET. SORRY FOR THE INCONVENIENCE.**

## Tools

* Neutron Quark Blockchain Explorer

## Node installation

Build and install neutron binary. 

```
$ git clone -b v0.0.1 git@github.com:neutron-org/neutron.git
$ cd neutron
$ make install
```

after installation please check installed version by running:

`neutrond version --long`

You should see something similar to the following:
```
name: neutrond
server_name: neutrond
version: 0.0.1
commit: 750ec1bfdc7831d2782e5d2109362a0874904cf6
``` 


## GenTx generation

### Init
```bash:
neutrond init "<moniker-name>" --chain-id quark-1
```

### Generate keys

```bash:
# To create new keypair - make sure you save the mnemonics!
neutrond keys add <key-name> 
```

or
```
# Restore existing odin wallet with mnemonic seed phrase. 
# You will be prompted to enter mnemonic seed. 
neutrond keys add <key-name> --recover
```
or
```
# Add keys using ledger
neutrond keys show <key-name> --ledger
```

Check your key:
```
# Query the keystore for your public address 
neutrond keys show <key-name> -a
```

### Create account to genesis

```
neutrond add-genesis-account <key-name> 1000000000untrn --keyring-backend os
```

### Create GenTX

```
# Create the gentx.
# Note, staking amount should be equal to 1000000000untrn.
neutrond gentx <key-name> 1000000000untrn --output-document=gentx.json \
  --chain-id=quark-1 \
  --moniker="<moniker-name>" \
  --website=<your-node-website> \
  --details=<your-node-details> \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --keyring-backend <os | file>
```

### Fill pull request description

To make the onboarding process simpler, please fill the description of your PR with the following data: <br/>
- Your validator's moniker
- Your webpage and/or active twitter page of your organization
- Each of your teammate's Discord handles <br/>
They will need to already be in this [Discord](https://discord.gg/r82yeMu9Rf) for us to give them the right roles! <br/>
- Your Neutronvaloper address

### Example of filled description field in gentx+peers PR

![Скриншот 03-11-2022 204428 1](https://user-images.githubusercontent.com/92199696/199796600-73f34a6f-c75c-4443-a598-21a50c067f91.png)
<br/>

### What to do next

After gentx is ready, please upload it to the https://github.com/neutron-org/testnets repository into `/quark/gentxs/` directory. Also please provide your peer and put it into  `/quark/peers/`, one peer per file.
