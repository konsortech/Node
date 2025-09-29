## CLI Command

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
gaiad keys add $WALLET
```

To recover your wallet using seed phrase
```
gaiad keys add $WALLET --recover
```

Show your wallet list
```
gaiad keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
COSMOS_WALLET_ADDRESS=$(gaiad keys show $WALLET -a)
COSMOS_VALOPER_ADDRESS=$(gaiad keys show $WALLET --bech val -a)
echo 'export COSMOS_WALLET_ADDRESS='${COSMOS_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export COSMOS_VALOPER_ADDRESS='${COSMOS_VALOPER_ADDRESS} >> $HOME/.bash_profile
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
gaiad query bank balances $COSMOS_WALLET_ADDRESS
```
To create your validator run command below
```
gaiad tx staking create-validator \
  --amount 1000000uatom \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.05" \
  --min-self-delegation "1" \
  --pubkey  $(gaiad tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $COSMOS_CHAIN_ID
```

### Check your validator key
```
[[ $(gaiad q staking validator $COSMOS_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(gaiad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
gaiad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu gaiad -o cat
```

Start service
```
sudo systemctl start gaiad
```

Stop service
```
sudo systemctl stop gaiad
```

Restart service
```
sudo systemctl restart gaiad
```

### Node info
Synchronization info
```
gaiad status 2>&1 | jq .SyncInfo
```

Validator info
```
gaiad status 2>&1 | jq .ValidatorInfo
```

Node info
```
gaiad status 2>&1 | jq .NodeInfo
```

Show node id
```
gaiad tendermint show-node-id
```

### Wallet operations
List of wallets
```
gaiad keys list
```

Recover wallet
```
gaiad keys add $WALLET --recover
```

Delete wallet
```
gaiad keys delete $WALLET
```

Get wallet balance
```
gaiad query bank balances $COSMOS_WALLET_ADDRESS
```

Transfer funds
```
gaiad tx bank send $COSMOS_WALLET_ADDRESS <TO_COSMOS_WALLET_ADDRESS> 1000000uatom
```

### Voting
```
gaiad tx gov vote 1 yes --from $WALLET --chain-id=$COSMOS_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
gaiad tx staking delegate $COSMOS_VALOPER_ADDRESS 1000000uatom --from=$WALLET --chain-id=$COSMOS_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
gaiad tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000uatom --from=$WALLET --chain-id=$COSMOS_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
gaiad tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$COSMOS_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
gaiad tx distribution withdraw-rewards $COSMOS_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$COSMOS_CHAIN_ID
```

### Validator management
Edit validator
```
gaiad tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$COSMOS_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
gaiad tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$COSMOS_CHAIN_ID \
  --gas=auto
```
