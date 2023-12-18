## Guidence for Create Validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
pryzmd keys add $WALLET
```

To recover your wallet using seed phrase
```
pryzmd keys add $WALLET --recover
```

Show your wallet list
```
pryzmd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
PRYZM_WALLET_ADDRESS=$(pryzmd keys show $WALLET -a)
PRYZM_VALOPER_ADDRESS=$(pryzmd keys show $WALLET --bech val -a)
echo 'export PRYZM_WALLET_ADDRESS='${PRYZM_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export PRYZM_VALOPER_ADDRESS='${PRYZM_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with discord faucet.
> [faucet](https://testnet.pryzm.zone/faucet)


### Create validator

check your wallet balance:
```
pryzmd query bank balances $PRYZM_WALLET_ADDRESS
```
To create your validator run command below
```
pryzmd tx staking create-validator \
  --amount 1000000upryzm \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(pryzmd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $PRYZM_CHAIN_ID
  --gas-adjustment 1.4 \
  --gas auto \
  --gas-prices 0.015upryzm \
  -y
```

### Check your validator key
```
[[ $(pryzmd q staking validator $PRYZM_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(pryzmd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
pryzmd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu pryzmd -o cat
```

Start service
```
sudo systemctl start pryzmd
```

Stop service
```
sudo systemctl stop pryzmd
```

Restart service
```
sudo systemctl restart pryzmd
```

### Node info
Synchronization info
```
pryzmd status 2>&1 | jq .SyncInfo
```

Validator info
```
pryzmd status 2>&1 | jq .ValidatorInfo
```

Node info
```
pryzmd status 2>&1 | jq .NodeInfo
```

Show node id
```
pryzmd tendermint show-node-id
```

### Wallet operations
List of wallets
```
pryzmd keys list
```

Recover wallet
```
pryzmd keys add $WALLET --recover
```

Delete wallet
```
pryzmd keys delete $WALLET
```

Get wallet balance
```
pryzmd query bank balances $PRYZM_WALLET_ADDRESS
```

Transfer funds
```
pryzmd tx bank send $PRYZM_WALLET_ADDRESS <TO_PRYZM_WALLET_ADDRESS> 1000000upryzm
```

### Voting
```
pryzmd tx gov vote 1 yes --from $WALLET --chain-id=$PRYZM_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
pryzmd tx staking delegate $PRYZM_VALOPER_ADDRESS 1000000upryzm --from=$WALLET --chain-id=$PRYZM_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
pryzmd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000upryzm --from=$WALLET --chain-id=$PRYZM_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
pryzmd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$PRYZM_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
pryzmd tx distribution withdraw-rewards $PRYZM_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$PRYZM_CHAIN_ID
```

### Validator management
Edit validator
```
pryzmd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$PRYZM_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
pryzmd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$PRYZM_CHAIN_ID \
  --gas=auto
```
