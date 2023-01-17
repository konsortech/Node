Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
marsd keys add $WALLET
```

To recover your wallet using seed phrase
```
marsd keys add $WALLET --recover
```

Show your wallet list
```
marsd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
MARS_WALLET_ADDRESS=$(marsd keys show $WALLET -a)
MARS_VALOPER_ADDRESS=$(marsd keys show $WALLET --bech val -a)
echo 'export MARS_WALLET_ADDRESS='${MARS_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export MARS_VALOPER_ADDRESS='${MARS_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet

```
https://faucet.marsprotocol.io/
```

### Create validator

check your wallet balance:
```
marsd query bank balances $MARS_WALLET_ADDRESS
```
To create your validator run command below
```
marsd tx staking create-validator \
  --amount 4000000umars \
  --from $WALLET \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(marsd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $MARS_CHAIN_ID
```

### Check your validator key
```
[[ $(marsd q staking validator $MARS_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(marsd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
marsd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu marsd -o cat
```

Start service
```
sudo systemctl start marsd
```

Stop service
```
sudo systemctl stop marsd
```

Restart service
```
sudo systemctl restart marsd
```

### Node info
Synchronization info
```
marsd status 2>&1 | jq .SyncInfo
```

Validator info
```
marsd status 2>&1 | jq .ValidatorInfo
```

Node info
```
marsd status 2>&1 | jq .NodeInfo
```

Show node id
```
marsd tendermint show-node-id
```

### Wallet operations
List of wallets
```
marsd keys list
```

Recover wallet
```
marsd keys add $WALLET --recover
```

Delete wallet
```
marsd keys delete $WALLET
```

Get wallet balance
```
marsd query bank balances $MARS_WALLET_ADDRESS
```

Transfer funds
```
marsd tx bank send $MARS_WALLET_ADDRESS <TO_MARS_WALLET_ADDRESS> 4000000umars
```

### Voting
```
marsd tx gov vote 1 yes --from $WALLET --chain-id=$MARS_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
marsd tx staking delegate $MARS_VALOPER_ADDRESS 4000000umars --from=$WALLET --chain-id=$MARS_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
marsd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 4000000umars --from=$WALLET --chain-id=$MARS_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
marsd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$MARS_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
marsd tx distribution withdraw-rewards $MARS_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$MARS_CHAIN_ID
```

### Validator management
Edit validator
```
marsd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$MARS_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
marsd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$MARS_CHAIN_ID \
  --gas=auto
```
