Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
acred keys add $WALLET
```

To recover your wallet using seed phrase
```
acred keys add $WALLET --recover
```

Show your wallet list
```
acred keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
ACRE_WALLET_ADDRESS=$(acred keys show $WALLET -a)
ACRE_VALOPER_ADDRESS=$(acred keys show $WALLET --bech val -a)
echo 'export ACRE_WALLET_ADDRESS='${ACRE_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export ACRE_VALOPER_ADDRESS='${ACRE_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet

```
https://faucet.marsprotocol.io/
```

### Create validator

check your wallet balance:
```
acred query bank balances $ACRE_WALLET_ADDRESS
```
To create your validator run command below
```
acred tx staking create-validator \
  --amount 5000000000000000000aacre \
  --from $WALLET \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(acred tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $ACRE_CHAIN_ID
```

### Check your validator key
```
[[ $(acred q staking validator $ACRE_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(acred status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
acred q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu acred -o cat
```

Start service
```
sudo systemctl start acred
```

Stop service
```
sudo systemctl stop acred
```

Restart service
```
sudo systemctl restart acred
```

### Node info
Synchronization info
```
acred status 2>&1 | jq .SyncInfo
```

Validator info
```
acred status 2>&1 | jq .ValidatorInfo
```

Node info
```
acred status 2>&1 | jq .NodeInfo
```

Show node id
```
acred tendermint show-node-id
```

### Wallet operations
List of wallets
```
acred keys list
```

Recover wallet
```
acred keys add $WALLET --recover
```

Delete wallet
```
acred keys delete $WALLET
```

Get wallet balance
```
acred query bank balances $ACRE_WALLET_ADDRESS
```

Transfer funds
```
acred tx bank send $ACRE_WALLET_ADDRESS <TO_ACRE_WALLET_ADDRESS> 5000000000000000000aacre
```

### Voting
```
acred tx gov vote 1 yes --from $WALLET --chain-id=$ACRE_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
acred tx staking delegate $ACRE_VALOPER_ADDRESS 5000000000000000000aacre --from=$WALLET --chain-id=$ACRE_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
acred tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 5000000000000000000aacre --from=$WALLET --chain-id=$ACRE_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
acred tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$ACRE_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
acred tx distribution withdraw-rewards $ACRE_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$ACRE_CHAIN_ID
```

### Validator management
Edit validator
```
acred tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$ACRE_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
acred tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$ACRE_CHAIN_ID \
  --gas=auto
```
