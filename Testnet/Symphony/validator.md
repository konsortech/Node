Guide for Validator CLI

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
symphonyd keys add $WALLET
```

To recover your wallet using seed phrase
```
symphonyd keys add $WALLET --recover
```

Show your wallet list
```
symphonyd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
SYMPHONY_WALLET_ADDRESS=$(symphonyd keys show $WALLET -a)
SYMPHONY_VALOPER_ADDRESS=$(symphonyd keys show $WALLET --bech val -a)
echo 'export SYMPHONY_WALLET_ADDRESS='${SYMPHONY_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export SYMPHONY_VALOPER_ADDRESS='${SYMPHONY_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Create validator

check your wallet balance:
```
symphonyd query bank balances $SYMPHONY_WALLET_ADDRESS
```
To create your validator run command below
```
symphonyd tx staking create-validator \
  --amount 1000000note \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(symphonyd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $SYMPHONY_CHAIN_ID
  --gas-adjustment 1.4 \
  --gas auto \
  --fees 800note
```

### Check your validator key
```
[[ $(symphonyd q staking validator $SYMPHONY_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(symphonyd status | jq -r .validator_info.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
symphonyd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu symphonyd -o cat
```

Start service
```
sudo systemctl start symphonyd
```

Stop service
```
sudo systemctl stop symphonyd
```

Restart service
```
sudo systemctl restart symphonyd
```

### Node info
Synchronization info
```
symphonyd status 2>&1 | jq .sync_info
```

Validator info
```
symphonyd status 2>&1 | jq .validator_info
```

Node info
```
symphonyd status 2>&1 | jq .node_info
```

Show node id
```
symphonyd tendermint show-node-id
```

### Wallet operations
List of wallets
```
symphonyd keys list
```

Recover wallet
```
symphonyd keys add $WALLET --recover
```

Delete wallet
```
symphonyd keys delete $WALLET
```

Get wallet balance
```
symphonyd query bank balances $SYMPHONY_WALLET_ADDRESS
```

Transfer funds
```
symphonyd tx bank send $SYMPHONY_WALLET_ADDRESS <TO_SYMPHONY_WALLET_ADDRESS> 1000000note
```

### Voting
```
symphonyd tx gov vote 1 yes --from $WALLET --chain-id=$SYMPHONY_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
symphonyd tx staking delegate $SYMPHONY_VALOPER_ADDRESS 1000000note --from=$WALLET --chain-id=$SYMPHONY_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
symphonyd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000note --from=$WALLET --chain-id=$SYMPHONY_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
symphonyd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$SYMPHONY_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
symphonyd tx distribution withdraw-rewards $SYMPHONY_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$SYMPHONY_CHAIN_ID
```

### Validator management
Edit validator
```
symphonyd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$SYMPHONY_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
symphonyd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$SYMPHONY_CHAIN_ID \
  --gas=auto
```
