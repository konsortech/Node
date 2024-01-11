## Guidence for Validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
artelad keys add $WALLET
```

To recover your wallet using seed phrase
```
artelad keys add $WALLET --recover
```

Show your wallet list
```
artelad keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
ARTELA_WALLET_ADDRESS=$(artelad keys show $WALLET -a)
ARTELA_VALOPER_ADDRESS=$(artelad keys show $WALLET --bech val -a)
echo 'export ARTELA_WALLET_ADDRESS='${ARTELA_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export ARTELA_VALOPER_ADDRESS='${ARTELA_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Create validator

check your wallet balance:
```
artelad query bank balances $ARTELA_WALLET_ADDRESS
```
To create your validator run command below
```
artelad tx staking create-validator \
  --amount 1000000000000000000uart \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(artelad tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $ARTELA_CHAIN_ID \
  --gas="200000"
```

### Check your validator key
```
[[ $(artelad q staking validator $ARTELA_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(artelad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
artelad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu artelad -o cat
```

Start service
```
sudo systemctl start artelad
```

Stop service
```
sudo systemctl stop artelad
```

Restart service
```
sudo systemctl restart artelad
```

### Node info
Synchronization info
```
artelad status 2>&1 | jq .SyncInfo
```

Validator info
```
artelad status 2>&1 | jq .ValidatorInfo
```

Node info
```
artelad status 2>&1 | jq .NodeInfo
```

Show node id
```
artelad tendermint show-node-id
```

### Wallet operations
List of wallets
```
artelad keys list
```

Recover wallet
```
artelad keys add $WALLET --recover
```

Delete wallet
```
artelad keys delete $WALLET
```

Get wallet balance
```
artelad query bank balances $ARTELA_WALLET_ADDRESS
```

Transfer funds
```
artelad tx bank send $ARTELA_WALLET_ADDRESS <TO_ARTELA_WALLET_ADDRESS> 1000000000000000000uart
```

### Voting
```
artelad tx gov vote 1 yes --from $WALLET --chain-id=$ARTELA_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
artelad tx staking delegate $ARTELA_VALOPER_ADDRESS 1000000000000000000uart --from=$WALLET --chain-id=$ARTELA_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
artelad tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000000000000000uart --from=$WALLET --chain-id=$ARTELA_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
artelad tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$ARTELA_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
artelad tx distribution withdraw-rewards $ARTELA_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$ARTELA_CHAIN_ID
```

### Validator management
Edit validator
```
artelad tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$ARTELA_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
artelad tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$ARTELA_CHAIN_ID \
  --gas=auto
```
