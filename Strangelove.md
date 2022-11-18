## Addresses
[Wallets](https://www.notion.so/strangeloveventures/ICS-Game-of-Chains-Wallet-1ec299135a6d47d48cac703b6f847479)
```
export ACCOUNT="neutron1z8aya3mhytqx2as22rjguu3v64x3h03xhkus24"
export VALOPER="neutronvaloper1z8aya3mhytqx2as22rjguu3v64x3h03xdt9fv3"
export VALCONSADDRESS="neutronvalcons169k95p42dkxv69fy68hczhsp74g6n02le9ttag"
export VALCONSPUBADDRESS="neutronvalconspub1zcjduepqpc9rfpjjnzkpfjjf29hgjlnumlalk69kfjzmfme6mrnt4v3u4yzqcv3xrx"
export VAL_PUBKEY=$(neutrond tendermint show-validator)
```
## Faucet
```
http://faucet.quark.ntrn.info
neutrond q bank balances ${ACCOUNT}
```

## Create Validator
```
echo password | neutrond tx staking create-validator \
--amount 1000000untrn \
--pubkey $VAL_PUBKEY \
--from $ACCOUNT \
--keyring-backend file \
--chain-id quark-1 \
--commission-max-change-rate 0.01 \
--commission-max-rate 0.2 \
--commission-rate 0.1 \
--moniker strangelove \
--commission-rate 0.10 \
--website https://strangelove.ventures \
--identity 158da6c7fcfb7bd23988d9c0d0d8b80f1c5c70b5 \
--min-self-delegation 1 \
--gas-prices 0.0025untrn --gas-adjustment 1.2 \
-b block -y
```

## Check Validator Set
```
neutrond q tendermint-validator-set | grep -A11 $VALCONSADDRESS
neutrond q tendermint-validator-set | grep -A11 $VALCONSADDRESS
```

## Delegate Tokens
```
echo password | neutrond tx staking \
delegate $VALOPER 1000000000untrn \
--from $ACCOUNT \
--keyring-backend file \
--chain-id quark-1 \
--gas-prices 0.0025untrn --gas-adjustment 1.2 \
-b block -y
```
## Vote on Proposals
```
echo password | neutrond tx gov vote 1 yes \
--from=$ACCOUNT \
--keyring-backend file \
--chain-id=quark-1 \
--gas-prices 0.0025prov --gas-adjustment 1.2 -y
```

neutrond tx gov vote 13 yes \\n--from=$ACCOUNT \\n--chain-id=quark-1 \\n--node http://35.203.137.156:26658 \\n--gas-prices 0.0025stake --gas-adjustment 1.2 -y

## Unbond
```
echo password | neutrond tx staking \
unbond $VALOPER 1000000stake \
--from="$ACCOUNT"  \
--gas-prices 0.0025stake --gas-adjustment 1.2 -y
```

## How to unjail
```
echo password | neutrond tx slashing unjail \
--from $ACCOUNT \
--gas-prices 0.0025stake --gas-adjustment 1.2 \
--yes
```

## Edit Validator
```
echo password | neutrond tx staking \
edit-validator "$VALOPER" \
--from cosmos1vsvn4fx0dkpxgq9vu9vkn6638gc888fd8xd5u4 \
--website https://strangelove.ventures/ \
--identity 158da6c7fcfb7bd23988d9c0d0d8b80f1c5c70b5 \
--moniker strangelove-ventures \
--commission-rate 0.13 \
--gas-prices 0.0025stake --gas-adjustment 1.2 --yes
```