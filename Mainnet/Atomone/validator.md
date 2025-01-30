## Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
atomoned keys add $WALLET
```

To recover your wallet using seed phrase
```
atomoned keys add $WALLET --recover
```

Show your wallet list
```
atomoned keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
ATOMONE_WALLET_ADDRESS=$(atomoned keys show $WALLET -a)
ATOMONE_VALOPER_ADDRESS=$(atomoned keys show $WALLET --bech val -a)
echo 'export ATOMONE_WALLET_ADDRESS='${ATOMONE_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export ATOMONE_VALOPER_ADDRESS='${ATOMONE_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet

```
Buy on Osmosis DEX
```

### Create validator

check your wallet balance:
```
atomoned query bank balances $ATOMONE_WALLET_ADDRESS
```
To create your validator run command below
```
atomoned tx staking create-validator \
  --amount 1000000uatone \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(atomoned tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $ATOMONE_CHAIN_ID
  --gas-prices=0.1uatone \
  --gas-adjustment=1.5 \
  --gas=auto
```

### Check your validator key
```
[[ $(atomoned q staking validator $ATOMONE_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(atomoned status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
atomoned q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu atomoned -o cat
```

Start service
```
sudo systemctl start atomoned
```

Stop service
```
sudo systemctl stop atomoned
```

Restart service
```
sudo systemctl restart atomoned
```

### Node info
Synchronization info
```
atomoned status 2>&1 | jq .SyncInfo
```

Validator info
```
atomoned status 2>&1 | jq .ValidatorInfo
```

Node info
```
atomoned status 2>&1 | jq .NodeInfo
```

Show node id
```
atomoned tendermint show-node-id
```

### Wallet operations
List of wallets
```
atomoned keys list
```

Recover wallet
```
atomoned keys add $WALLET --recover
```

Delete wallet
```
atomoned keys delete $WALLET
```

Get wallet balance
```
atomoned query bank balances $ATOMONE_WALLET_ADDRESS
```

Transfer funds
```
atomoned tx bank send $ATOMONE_WALLET_ADDRESS <TO_ATOMONE_WALLET_ADDRESS> 1000000uatone
```

### Voting
```
atomoned tx gov vote 1 yes --from $WALLET --chain-id=$ATOMONE_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
atomoned tx staking delegate $ATOMONE_VALOPER_ADDRESS 1000000uatone --from=$WALLET --chain-id=$ATOMONE_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
atomoned tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000uatone --from=$WALLET --chain-id=$ATOMONE_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
atomoned tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$ATOMONE_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
atomoned tx distribution withdraw-rewards $ATOMONE_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$ATOMONE_CHAIN_ID
```

### Validator management
Edit validator
```
atomoned tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$ATOMONE_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
atomoned tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$ATOMONE_CHAIN_ID \
  --gas=auto
```
