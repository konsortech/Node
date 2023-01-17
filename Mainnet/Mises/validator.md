Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
misestmd keys add $WALLET
```

To recover your wallet using seed phrase
```
misestmd keys add $WALLET --recover
```

Show your wallet list
```
misestmd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
MISES_WALLET_ADDRESS=$(misestmd keys show $WALLET -a)
MISES_VALOPER_ADDRESS=$(misestmd keys show $WALLET --bech val -a)
echo 'export MISES_WALLET_ADDRESS='${MISES_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export MISES_VALOPER_ADDRESS='${MISES_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with buy MIS token.
```
N/A
```

### Create validator

check your wallet balance:
```
misestmd query bank balances $MISES_WALLET_ADDRESS
```
To create your validator run command below
```
misestmd tx staking create-validator \
  --amount 1000000umis \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(misestmd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $MISES_CHAIN_ID
  --gas auto
  --node tcp://127.0.0.1:26657
```

### Check your validator key
```
[[ $(misestmd q staking validator $MISES_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(misestmd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
misestmd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu misestmd -o cat
```

Start service
```
sudo systemctl start misestmd
```

Stop service
```
sudo systemctl stop misestmd
```

Restart service
```
sudo systemctl restart misestmd
```

### Node info
Synchronization info
```
misestmd status 2>&1 | jq .SyncInfo
```

Validator info
```
misestmd status 2>&1 | jq .ValidatorInfo
```

Node info
```
misestmd status 2>&1 | jq .NodeInfo
```

Show node id
```
misestmd tendermint show-node-id
```

### Wallet operations
List of wallets
```
misestmd keys list
```

Recover wallet
```
misestmd keys add $WALLET --recover
```

Delete wallet
```
misestmd keys delete $WALLET
```

Get wallet balance
```
misestmd query bank balances $MISES_WALLET_ADDRESS
```

Transfer funds
```
misestmd tx bank send $MISES_WALLET_ADDRESS <TO_MISES_WALLET_ADDRESS> 1000000umis
```

### Voting
```
misestmd tx gov vote 1 yes --from $WALLET --chain-id=$MISES_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
misestmd tx staking delegate $MISES_VALOPER_ADDRESS 1000000umis --from=$WALLET --chain-id=$MISES_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
misestmd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000umis --from=$WALLET --chain-id=$MISES_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
misestmd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$MISES_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
misestmd tx distribution withdraw-rewards $MISES_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$MISES_CHAIN_ID
```

### Validator management
Edit validator
```
misestmd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$MISES_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
misestmd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$MISES_CHAIN_ID \
  --gas=auto
```
