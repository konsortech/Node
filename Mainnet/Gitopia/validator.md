Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
gitopiad keys add $WALLET
```

To recover your wallet using seed phrase
```
gitopiad keys add $WALLET --recover
```

Show your wallet list
```
gitopiad keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
GITOPIA_WALLET_ADDRESS=$(gitopiad keys show $WALLET -a)
GITOPIA_VALOPER_ADDRESS=$(gitopiad keys show $WALLET --bech val -a)
echo 'export GITOPIA_WALLET_ADDRESS='${GITOPIA_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export GITOPIA_VALOPER_ADDRESS='${GITOPIA_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet

```
N/A
```

### Create validator

check your wallet balance:
```
gitopiad query bank balances $GITOPIA_WALLET_ADDRESS
```
To create your validator run command below
```
gitopiad tx staking create-validator \
  --amount 1000000utlore \
  --from $WALLET \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(gitopiad tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $GITOPIA_CHAIN_ID
```

### Check your validator key
```
[[ $(gitopiad q staking validator $GITOPIA_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(gitopiad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
gitopiad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu gitopiad -o cat
```

Start service
```
sudo systemctl start gitopiad
```

Stop service
```
sudo systemctl stop gitopiad
```

Restart service
```
sudo systemctl restart gitopiad
```

### Node info
Synchronization info
```
gitopiad status 2>&1 | jq .SyncInfo
```

Validator info
```
gitopiad status 2>&1 | jq .ValidatorInfo
```

Node info
```
gitopiad status 2>&1 | jq .NodeInfo
```

Show node id
```
gitopiad tendermint show-node-id
```

### Wallet operations
List of wallets
```
gitopiad keys list
```

Recover wallet
```
gitopiad keys add $WALLET --recover
```

Delete wallet
```
gitopiad keys delete $WALLET
```

Get wallet balance
```
gitopiad query bank balances $GITOPIA_WALLET_ADDRESS
```

Transfer funds
```
gitopiad tx bank send $GITOPIA_WALLET_ADDRESS <TO_GITOPIA_WALLET_ADDRESS> 1000000utlore
```

### Voting
```
gitopiad tx gov vote 1 yes --from $WALLET --chain-id=$GITOPIA_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
gitopiad tx staking delegate $GITOPIA_VALOPER_ADDRESS 1000000utlore --from=$WALLET --chain-id=$GITOPIA_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
gitopiad tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000utlore --from=$WALLET --chain-id=$GITOPIA_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
gitopiad tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$GITOPIA_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
gitopiad tx distribution withdraw-rewards $GITOPIA_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$GITOPIA_CHAIN_ID
```

### Validator management
Edit validator
```
gitopiad tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$GITOPIA_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
gitopiad tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$GITOPIA_CHAIN_ID \
  --gas=auto
```
