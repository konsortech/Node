Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
bcnad keys add $WALLET
```

To recover your wallet using seed phrase
```
bcnad keys add $WALLET --recover
```

Show your wallet list
```
bcnad keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
BITCANNA_WALLET_ADDRESS=$(bcnad keys show $WALLET -a)
BITCANNA_VALOPER_ADDRESS=$(bcnad keys show $WALLET --bech val -a)
echo 'export BITCANNA_WALLET_ADDRESS='${BITCANNA_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export BITCANNA_VALOPER_ADDRESS='${BITCANNA_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with buy bcna token.
```
Go to Discord channel #devnet-faucet
```

### Create validator

check your wallet balance:
```
bcnad query bank balances $BITCANNA_WALLET_ADDRESS
```
To create your validator run command below
```
bcnad tx staking create-validator \
  --amount 1000000ubcna \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(bcnad tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $BITCANNA_CHAIN_ID
  --gas-prices=0.1ubcna \
  --gas-adjustment=1.5 \
  --gas=auto \
```

### Check your validator key
```
[[ $(bcnad q staking validator $BITCANNA_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(bcnad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
bcnad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu bcnad -o cat
```

Start service
```
sudo systemctl start bcnad
```

Stop service
```
sudo systemctl stop bcnad
```

Restart service
```
sudo systemctl restart bcnad
```

### Node info
Synchronization info
```
bcnad status 2>&1 | jq .SyncInfo
```

Validator info
```
bcnad status 2>&1 | jq .ValidatorInfo
```

Node info
```
bcnad status 2>&1 | jq .NodeInfo
```

Show node id
```
bcnad tendermint show-node-id
```

### Wallet operations
List of wallets
```
bcnad keys list
```

Recover wallet
```
bcnad keys add $WALLET --recover
```

Delete wallet
```
bcnad keys delete $WALLET
```

Get wallet balance
```
bcnad query bank balances $BITCANNA_WALLET_ADDRESS
```

Transfer funds
```
bcnad tx bank send $BITCANNA_WALLET_ADDRESS <TO_BITCANNA_WALLET_ADDRESS> 10000000ubcna
```

### Voting
```
bcnad tx gov vote 1 yes --from $WALLET --chain-id=$BITCANNA_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
bcnad tx staking delegate $BITCANNA_VALOPER_ADDRESS 10000000ubcna --from=$WALLET --chain-id=$BITCANNA_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
bcnad tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000ubcna --from=$WALLET --chain-id=$BITCANNA_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
bcnad tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$BITCANNA_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
bcnad tx distribution withdraw-rewards $BITCANNA_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$BITCANNA_CHAIN_ID
```

### Validator management
Edit validator
```
bcnad tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$BITCANNA_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
bcnad tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$BITCANNA_CHAIN_ID \
  --gas=auto
```
