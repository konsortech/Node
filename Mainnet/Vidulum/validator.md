Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
vidulumd keys add $WALLET
```

To recover your wallet using seed phrase
```
vidulumd keys add $WALLET --recover
```

Show your wallet list
```
vidulumd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
VIDULUM_WALLET_ADDRESS=$(vidulumd keys show $WALLET -a)
VIDULUM_VALOPER_ADDRESS=$(vidulumd keys show $WALLET --bech val -a)
echo 'export VIDULUM_WALLET_ADDRESS='${VIDULUM_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export VIDULUM_VALOPER_ADDRESS='${VIDULUM_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet

```
swap osmos <> VDL
https://frontier.osmosis.zone/
```

### Create validator

check your wallet balance:
```
vidulumd query bank balances $VIDULUM_WALLET_ADDRESS
```
To create your validator run command below
```
vidulumd tx staking create-validator \
  --amount 10000000uvdl \
  --from $WALLET \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(vidulumd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $VIDULUM_CHAIN_ID
```

### Check your validator key
```
[[ $(vidulumd q staking validator $VIDULUM_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(vidulumd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
vidulumd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu vidulumd -o cat
```

Start service
```
sudo systemctl start vidulumd
```

Stop service
```
sudo systemctl stop vidulumd
```

Restart service
```
sudo systemctl restart vidulumd
```

### Node info
Synchronization info
```
vidulumd status 2>&1 | jq .SyncInfo
```

Validator info
```
vidulumd status 2>&1 | jq .ValidatorInfo
```

Node info
```
vidulumd status 2>&1 | jq .NodeInfo
```

Show node id
```
vidulumd tendermint show-node-id
```

### Wallet operations
List of wallets
```
vidulumd keys list
```

Recover wallet
```
vidulumd keys add $WALLET --recover
```

Delete wallet
```
vidulumd keys delete $WALLET
```

Get wallet balance
```
vidulumd query bank balances $VIDULUM_WALLET_ADDRESS
```

Transfer funds
```
vidulumd tx bank send $VIDULUM_WALLET_ADDRESS <TO_VIDULUM_WALLET_ADDRESS> 10000000uvdl
```

### Voting
```
vidulumd tx gov vote 1 yes --from $WALLET --chain-id=$VIDULUM_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
vidulumd tx staking delegate $VIDULUM_VALOPER_ADDRESS 10000000uvdl --from=$WALLET --chain-id=$VIDULUM_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
vidulumd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000uvdl --from=$WALLET --chain-id=$VIDULUM_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
vidulumd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$VIDULUM_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
vidulumd tx distribution withdraw-rewards $VIDULUM_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$VIDULUM_CHAIN_ID
```

### Validator management
Edit validator
```
vidulumd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$VIDULUM_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
vidulumd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$VIDULUM_CHAIN_ID \
  --gas=auto
```
