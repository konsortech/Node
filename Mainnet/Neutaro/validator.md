## Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
Neutaro keys add $WALLET
```

To recover your wallet using seed phrase
```
Neutaro keys add $WALLET --recover
```

Show your wallet list
```
Neutaro keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
NEUTARO_WALLET_ADDRESS=$(Neutaro keys show $WALLET -a)
NEUTARO_VALOPER_ADDRESS=$(Neutaro keys show $WALLET --bech val -a)
echo 'export NEUTARO_WALLET_ADDRESS='${NEUTARO_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export NEUTARO_VALOPER_ADDRESS='${NEUTARO_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Create validator

check your wallet balance:
```
Neutaro query bank balances $NEUTARO_WALLET_ADDRESS
```
To create your validator run command below
```
Neutaro tx staking create-validator \
  --amount 1000000uneutaro \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1000000" \
  --pubkey  $(Neutaro tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $NEUTARO_CHAIN_ID \
  --gas=”auto” \
  --gas-prices=”0.0025uneutaro” \
  --gas-adjustment=”1.5″
```

### Check your validator key
```
[[ $(Neutaro q staking validator $NEUTARO_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(Neutaro status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
Neutaro q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu Neutaro -o cat
```

Start service
```
sudo systemctl start Neutaro
```

Stop service
```
sudo systemctl stop Neutaro
```

Restart service
```
sudo systemctl restart Neutaro
```

### Node info
Synchronization info
```
Neutaro status 2>&1 | jq .SyncInfo
```

Validator info
```
Neutaro status 2>&1 | jq .ValidatorInfo
```

Node info
```
Neutaro status 2>&1 | jq .NodeInfo
```

Show node id
```
Neutaro tendermint show-node-id
```

### Wallet operations
List of wallets
```
Neutaro keys list
```

Recover wallet
```
Neutaro keys add $WALLET --recover
```

Delete wallet
```
Neutaro keys delete $WALLET
```

Get wallet balance
```
Neutaro query bank balances $NEUTARO_WALLET_ADDRESS
```

Transfer funds
```
Neutaro tx bank send $NEUTARO_WALLET_ADDRESS <TO_NEUTARO_WALLET_ADDRESS> 1000000uneutaro
```

### Voting
```
Neutaro tx gov vote 1 yes --from $WALLET --chain-id=$NEUTARO_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
Neutaro tx staking delegate $NEUTARO_VALOPER_ADDRESS 1000000uneutaro --from=$WALLET --chain-id=$NEUTARO_CHAIN_ID --gas=”auto" --gas-prices=”0.0025uneutaro” --gas-adjustment=”1.5″
```

Redelegate stake from validator to another validator
```
Neutaro tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000uneutaro --from=$WALLET --chain-id=$NEUTARO_CHAIN_ID --gas=”auto" --gas-prices=”0.0025uneutaro” --gas-adjustment=”1.5″
```

Withdraw all rewards
```
Neutaro tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$NEUTARO_CHAIN_ID --gas=”auto" --gas-prices=”0.0025uneutaro” --gas-adjustment=”1.5″
```

Withdraw rewards with commision
```
Neutaro tx distribution withdraw-rewards $NEUTARO_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$NEUTARO_CHAIN_ID --gas=”auto" --gas-prices=”0.0025uneutaro” --gas-adjustment=”1.5″
```

### Validator management
Edit validator
```
Neutaro tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$NEUTARO_CHAIN_ID \
  --from=$WALLET \
  --gas=”auto" \
  --gas-prices=”0.0025uneutaro” \
  --gas-adjustment=”1.5″
```

Unjail validator
```
Neutaro tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$NEUTARO_CHAIN_ID \
  --gas=”auto" \
  --gas-prices=”0.0025uneutaro” \
  --gas-adjustment=”1.5″
```
