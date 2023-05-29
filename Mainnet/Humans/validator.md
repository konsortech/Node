Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
humansd keys add $WALLET
```

To recover your wallet using seed phrase
```
humansd keys add $WALLET --recover
```

Show your wallet list
```
humansd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
HUMANS_WALLET_ADDRESS=$(humansd keys show $WALLET -a)
HUMANS_VALOPER_ADDRESS=$(humansd keys show $WALLET --bech val -a)
echo 'export HUMANS_WALLET_ADDRESS='${HUMANS_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export HUMANS_VALOPER_ADDRESS='${HUMANS_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet

```
Buy on Kucoin
```

### Create validator

check your wallet balance:
```
humansd query bank balances $HUMANS_WALLET_ADDRESS
```
To create your validator run command below
```
humansd tx staking create-validator \
  --amount 1000000aheart \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(humansd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $HUMANS_CHAIN_ID
  --gas-prices=20000000000aheart \
  --gas-adjustment=1.5 \
  --gas=auto \
```

### Check your validator key
```
[[ $(humansd q staking validator $HUMANS_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(humansd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
humansd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu humansd -o cat
```

Start service
```
sudo systemctl start humansd
```

Stop service
```
sudo systemctl stop humansd
```

Restart service
```
sudo systemctl restart humansd
```

### Node info
Synchronization info
```
humansd status 2>&1 | jq .SyncInfo
```

Validator info
```
humansd status 2>&1 | jq .ValidatorInfo
```

Node info
```
humansd status 2>&1 | jq .NodeInfo
```

Show node id
```
humansd tendermint show-node-id
```

### Wallet operations
List of wallets
```
humansd keys list
```

Recover wallet
```
humansd keys add $WALLET --recover
```

Delete wallet
```
humansd keys delete $WALLET
```

Get wallet balance
```
humansd query bank balances $HUMANS_WALLET_ADDRESS
```

Transfer funds
```
humansd tx bank send $HUMANS_WALLET_ADDRESS <TO_HUMANS_WALLET_ADDRESS> 1000000aheart
```

### Voting
```
humansd tx gov vote 1 yes --from $WALLET --chain-id=$HUMANS_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
humansd tx staking delegate $HUMANS_VALOPER_ADDRESS 1000000aheart --from=$WALLET --chain-id=$HUMANS_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
humansd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000aheart --from=$WALLET --chain-id=$HUMANS_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
humansd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$HUMANS_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
humansd tx distribution withdraw-rewards $HUMANS_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$HUMANS_CHAIN_ID
```

### Validator management
Edit validator
```
humansd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$HUMANS_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
humansd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$HUMANS_CHAIN_ID \
  --gas=auto
```
