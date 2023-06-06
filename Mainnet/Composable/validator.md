Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
banksyd keys add $WALLET
```

To recover your wallet using seed phrase
```
banksyd keys add $WALLET --recover
```

Show your wallet list
```
banksyd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
COMPOSABLE_WALLET_ADDRESS=$(banksyd keys show $WALLET -a)
COMPOSABLE_VALOPER_ADDRESS=$(banksyd keys show $WALLET --bech val -a)
echo 'export COMPOSABLE_WALLET_ADDRESS='${COMPOSABLE_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export COMPOSABLE_VALOPER_ADDRESS='${COMPOSABLE_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with discord faucet.
```
N/A
```

### Create validator

check your wallet balance:
```
banksyd query bank balances $COMPOSABLE_WALLET_ADDRESS
```
To create your validator run command below
```
banksyd tx staking create-validator \
  --amount 50000000000000ppica \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(banksyd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $COMPOSABLE_CHAIN_ID
```

### Check your validator key
```
[[ $(banksyd q staking validator $COMPOSABLE_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(banksyd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
banksyd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu banksyd -o cat
```

Start service
```
sudo systemctl start banksyd
```

Stop service
```
sudo systemctl stop banksyd
```

Restart service
```
sudo systemctl restart banksyd
```

### Node info
Synchronization info
```
banksyd status 2>&1 | jq .SyncInfo
```

Validator info
```
banksyd status 2>&1 | jq .ValidatorInfo
```

Node info
```
banksyd status 2>&1 | jq .NodeInfo
```

Show node id
```
banksyd tendermint show-node-id
```

### Wallet operations
List of wallets
```
banksyd keys list
```

Recover wallet
```
banksyd keys add $WALLET --recover
```

Delete wallet
```
banksyd keys delete $WALLET
```

Get wallet balance
```
banksyd query bank balances $COMPOSABLE_WALLET_ADDRESS
```

Transfer funds
```
banksyd tx bank send $COMPOSABLE_WALLET_ADDRESS <TO_COMPOSABLE_WALLET_ADDRESS> 1000000ppica 
```

### Voting
```
banksyd tx gov vote 1 yes --from $WALLET --chain-id=$COMPOSABLE_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
banksyd tx staking delegate $COMPOSABLE_VALOPER_ADDRESS 1000000ppica  --from=$WALLET --chain-id=$COMPOSABLE_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
banksyd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000ppica --from=$WALLET --chain-id=$COMPOSABLE_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
banksyd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$COMPOSABLE_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
banksyd tx distribution withdraw-rewards $COMPOSABLE_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$COMPOSABLE_CHAIN_ID
```

### Validator management
Edit validator
```
banksyd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$COMPOSABLE_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
banksyd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$COMPOSABLE_CHAIN_ID \
  --gas=auto
```
