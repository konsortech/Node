Guide for Node CLI

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
emped keys add $WALLET
```

To recover your wallet using seed phrase
```
emped keys add $WALLET --recover
```

Show your wallet list
```
emped keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
EMPEIRIA_WALLET_ADDRESS=$(emped keys show $WALLET -a)
EMPEIRIA_VALOPER_ADDRESS=$(emped keys show $WALLET --bech val -a)
echo 'export EMPEIRIA_WALLET_ADDRESS='${EMPEIRIA_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export EMPEIRIA_VALOPER_ADDRESS='${EMPEIRIA_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Create validator

check your wallet balance:
```
emped query bank balances $EMPEIRIA_WALLET_ADDRESS
```
To create your validator run command below
```
emped tx staking create-validator \
  --amount 1000000uempe \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey $(emped tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $EMPEIRIA_CHAIN_ID
  --gas-prices 0.001uempe \
  --gas "auto" \
  --gas-adjustment "1.5"
```

### Check your validator key
```
[[ $(emped q staking validator $EMPEIRIA_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(emped status | jq -r .validator_info.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
emped q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu emped -o cat
```

Start service
```
sudo systemctl start emped
```

Stop service
```
sudo systemctl stop emped
```

Restart service
```
sudo systemctl restart emped
```

### Node info
Synchronization info
```
emped status 2>&1 | jq .sync_info
```

Validator info
```
emped status 2>&1 | jq .validator_info
```

Node info
```
emped status 2>&1 | jq .node_info
```

Show node id
```
emped tendermint show-node-id
```

### Wallet operations
List of wallets
```
emped keys list
```

Recover wallet
```
emped keys add $WALLET --recover
```

Delete wallet
```
emped keys delete $WALLET
```

Get wallet balance
```
emped query bank balances $EMPEIRIA_WALLET_ADDRESS
```

Transfer funds
```
emped tx bank send $EMPEIRIA_WALLET_ADDRESS <TO_EMPEIRIA_WALLET_ADDRESS> 1000000uempe
```

### Voting
```
emped tx gov vote 1 yes --from $WALLET --chain-id=$EMPEIRIA_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
emped tx staking delegate $EMPEIRIA_VALOPER_ADDRESS 1000000uempe --from=$WALLET --chain-id=$EMPEIRIA_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
emped tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000uempe --from=$WALLET --chain-id=$EMPEIRIA_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
emped tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$EMPEIRIA_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
emped tx distribution withdraw-rewards $EMPEIRIA_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$EMPEIRIA_CHAIN_ID
```

### Validator management
Edit validator
```
emped tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$EMPEIRIA_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
emped tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$EMPEIRIA_CHAIN_ID \
  --gas=auto
```
