Additional info: https://github.com/cosmos/testnets/blob/master/replicated-security/CONSUMER_LAUNCH_GUIDE.md

## Create consumer addition proposal

`gaiad tx gov submit-proposal consumer-addition proposal-reh-neutron-1.json --from <key name> --chain-id rehearsal-1 --broadcast-mode block --gas 500000`
`gaiad tx gov vote <proposal id> yes --from <key name> --chain-id rehearsal-1 --broadcast-mode block`


## Consumer genesis

Get from provider chain, this command will return genesis after proposal will pass and spawn time will pass

`gaiad q provider consumer-genesis test-1 -o json > ccv-state.json`


Add to the network genesis

`jq -s '.[0].app_state.ccvconsumer = .[1] | .[0]' neutron-genesis.json ccv-state.json > neutron-genesis-ccv.json`