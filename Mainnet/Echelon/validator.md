Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
echo "export WALLET=wallet" >> $HOME/.bash_profile
source $HOME/.bash_profile

echelond keys add $WALLET
```

To recover your wallet using seed phrase
```
echelond keys add $WALLET --recover
```

Show your wallet list
```
echelond keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
ECHELON_WALLET_ADDRESS=$(echelond keys show $WALLET -a)
ECHELON_VALOPER_ADDRESS=$(echelond keys show $WALLET --bech val -a)
echo 'export ECHELON_WALLET_ADDRESS='${ECHELON_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export ECHELON_VALOPER_ADDRESS='${ECHELON_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with discord faucet.
```
-
```

### Create validator

check your wallet balance:
```
echelond query bank balances $ECHELON_WALLET_ADDRESS
```
To create your validator run command below
```
echelond tx staking create-validator \
  --from $WALLET \
  --amount="1000000uechelon" \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --pubkey=$(echelond tendermint show-validator) \
  --moniker="$MONIKER" \
  --identity="<your_keybase>" \
  --details="<your_detail>" \
  --website="<your_web>" \
  --min-self-delegation "1" \
  --chain-id=$ECHELON_CHAIN_ID \
  --fees 2500uechelon
```

### Check your validator key
```
[[ $(echelond q staking validator $ECHELON_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(echelond status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
echelond q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu echelond -o cat
```

Start service
```
sudo systemctl start 8ball
```

Stop service
```
sudo systemctl stop 8ball
```

Restart service
```
sudo systemctl restart 8ball
```

### Node info
Synchronization info
```
echelond status 2>&1 | jq .SyncInfo
```

Validator info
```
echelond status 2>&1 | jq .ValidatorInfo
```

Node info
```
echelond status 2>&1 | jq .NodeInfo
```

Show node id
```
echelond tendermint show-node-id
```

### Wallet operations
List of wallets
```
echelond keys list
```

Recover wallet
```
echelond keys add $WALLET --recover
```

Delete wallet
```
echelond keys delete $WALLET
```

Get wallet balance
```
echelond query bank balances $ECHELON_WALLET_ADDRESS
```

Transfer funds
```
echelond tx bank send $ECHELON_WALLET_ADDRESS <TO_ECHELON_WALLET_ADDRESS> 100000uebl
```

### Voting
```
echelond tx gov vote 1 yes --from $WALLET --chain-id=$ECHELON_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
echelond tx staking delegate $ECHELON_VALOPER_ADDRESS 100000uebl --from=$WALLET --chain-id=$ECHELON_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
echelond tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 100000uebl --from=$WALLET --chain-id=$ECHELON_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
echelond tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$ECHELON_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
echelond tx distribution withdraw-rewards $ECHELON_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$ECHELON_CHAIN_ID
```

### Validator management
Edit validator
```
echelond tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$ECHELON_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
echelond tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$ECHELON_CHAIN_ID \
  --gas=auto
```
