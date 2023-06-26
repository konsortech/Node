Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
noriad keys add $WALLET
```

To recover your wallet using seed phrase
```
noriad keys add $WALLET --recover
```

Show your wallet list
```
noriad keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
NORIA_WALLET_ADDRESS=$(noriad keys show $WALLET -a)
NORIA_VALOPER_ADDRESS=$(noriad keys show $WALLET --bech val -a)
echo 'export NORIA_WALLET_ADDRESS='${NORIA_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export NORIA_VALOPER_ADDRESS='${NORIA_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with discord faucet.
```
Go to #noria-faucet on discord channel and filled noria address
To get unoria, either swap some of your ucrd on the dex: https://macro.noria.network/
```

### Create validator

check your wallet balance:
```
noriad query bank balances $NORIA_WALLET_ADDRESS
```
To create your validator run command below
```
noriad tx staking create-validator \
  --amount 1000000unoria \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.05" \
  --min-self-delegation "1" \
  --pubkey  $(noriad tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $NORIA_CHAIN_ID\
  --gas-adjustment 1.4 \
  --gas auto \
  --gas-prices 0.0025ucrd 
```

### Check your validator key
```
[[ $(noriad q staking validator $NORIA_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(noriad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
noriad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu noriad -o cat
```

Start service
```
sudo systemctl start noriad
```

Stop service
```
sudo systemctl stop noriad
```

Restart service
```
sudo systemctl restart noriad
```

### Node info
Synchronization info
```
noriad status 2>&1 | jq .SyncInfo
```

Validator info
```
noriad status 2>&1 | jq .ValidatorInfo
```

Node info
```
noriad status 2>&1 | jq .NodeInfo
```

Show node id
```
noriad tendermint show-node-id
```

### Wallet operations
List of wallets
```
noriad keys list
```

Recover wallet
```
noriad keys add $WALLET --recover
```

Delete wallet
```
noriad keys delete $WALLET
```

Get wallet balance
```
noriad query bank balances $NORIA_WALLET_ADDRESS
```

Transfer funds
```
noriad tx bank send $NORIA_WALLET_ADDRESS <TO_NORIA_WALLET_ADDRESS> 1000000unoria 
```

### Voting
```
noriad tx gov vote 1 yes --from $WALLET --chain-id=$NORIA_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
noriad tx staking delegate $NORIA_VALOPER_ADDRESS 1000000unoria  --from=$WALLET --chain-id=$NORIA_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
noriad tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000unoria  --from=$WALLET --chain-id=$NORIA_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
noriad tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$NORIA_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
noriad tx distribution withdraw-rewards $NORIA_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$NORIA_CHAIN_ID
```

### Validator management
Edit validator
```
noriad tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$NORIA_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
noriad tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$NORIA_CHAIN_ID \
  --gas=auto
```
