Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
iris keys add $WALLET
```

To recover your wallet using seed phrase
```
iris keys add $WALLET --recover
```

Show your wallet list
```
iris keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
IRIS_WALLET_ADDRESS=$(iris keys show $WALLET -a)
IRIS_VALOPER_ADDRESS=$(iris keys show $WALLET --bech val -a)
echo 'export IRIS_WALLET_ADDRESS='${IRIS_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export IRIS_VALOPER_ADDRESS='${IRIS_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with discord faucet.
```
N/A
```

### Create validator

check your wallet balance:
```
iris query bank balances $IRIS_WALLET_ADDRESS
```
To create your validator run command below
```
iris tx staking create-validator \
  --amount 1000000uiris \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(iris tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $IRIS_CHAIN_ID
```

### Check your validator key
```
[[ $(iris q staking validator $IRIS_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(iris status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
iris q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu iris -o cat
```

Start service
```
sudo systemctl start iris
```

Stop service
```
sudo systemctl stop iris
```

Restart service
```
sudo systemctl restart iris
```

### Node info
Synchronization info
```
iris status 2>&1 | jq .SyncInfo
```

Validator info
```
iris status 2>&1 | jq .ValidatorInfo
```

Node info
```
iris status 2>&1 | jq .NodeInfo
```

Show node id
```
iris tendermint show-node-id
```

### Wallet operations
List of wallets
```
iris keys list
```

Recover wallet
```
iris keys add $WALLET --recover
```

Delete wallet
```
iris keys delete $WALLET
```

Get wallet balance
```
iris query bank balances $IRIS_WALLET_ADDRESS
```

Transfer funds
```
iris tx bank send $IRIS_WALLET_ADDRESS <TO_IRIS_WALLET_ADDRESS> 1000000uiris
```

### Voting
```
iris tx gov vote 1 yes --from $WALLET --chain-id=$IRIS_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
iris tx staking delegate $IRIS_VALOPER_ADDRESS 1000000uiris --from=$WALLET --chain-id=$IRIS_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
iris tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000uiris --from=$WALLET --chain-id=$IRIS_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
iris tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$IRIS_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
iris tx distribution withdraw-rewards $IRIS_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$IRIS_CHAIN_ID
```

### Validator management
Edit validator
```
iris tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$IRIS_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
iris tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$IRIS_CHAIN_ID \
  --gas=auto
```
