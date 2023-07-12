Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
mund keys add $WALLET
```

To recover your wallet using seed phrase
```
mund keys add $WALLET --recover
```

Show your wallet list
```
mund keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
MUN_WALLET_ADDRESS=$(mund keys show $WALLET -a)
MUN_VALOPER_ADDRESS=$(mund keys show $WALLET --bech val -a)
echo 'export MUN_WALLET_ADDRESS='${MUN_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export MUN_VALOPER_ADDRESS='${MUN_VALOPER_ADDRESS} >> $HOME/.bash_profile
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
mund query bank balances $MUN_WALLET_ADDRESS
```
To create your validator run command below
```
mund tx staking create-validator \
  --amount 1000000tmun \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(mund tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $MUN_CHAIN_ID
```

### Check your validator key
```
[[ $(mund q staking validator $MUN_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(mund status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
mund q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu mund -o cat
```

Start service
```
sudo systemctl start mund
```

Stop service
```
sudo systemctl stop mund
```

Restart service
```
sudo systemctl restart mund
```

### Node info
Synchronization info
```
mund status 2>&1 | jq .SyncInfo
```

Validator info
```
mund status 2>&1 | jq .ValidatorInfo
```

Node info
```
mund status 2>&1 | jq .NodeInfo
```

Show node id
```
mund tendermint show-node-id
```

### Wallet operations
List of wallets
```
mund keys list
```

Recover wallet
```
mund keys add $WALLET --recover
```

Delete wallet
```
mund keys delete $WALLET
```

Get wallet balance
```
mund query bank balances $MUN_WALLET_ADDRESS
```

Transfer funds
```
mund tx bank send $MUN_WALLET_ADDRESS <TO_MUN_WALLET_ADDRESS> 1000000tmun
```

### Voting
```
mund tx gov vote 1 yes --from $WALLET --chain-id=$MUN_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
mund tx staking delegate $MUN_VALOPER_ADDRESS 1000000tmun --from=$WALLET --chain-id=$MUN_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
mund tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000tmun --from=$WALLET --chain-id=$MUN_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
mund tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$MUN_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
mund tx distribution withdraw-rewards $MUN_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$MUN_CHAIN_ID
```

### Validator management
Edit validator
```
mund tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$MUN_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
mund tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$MUN_CHAIN_ID \
  --gas=auto
```
