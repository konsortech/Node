Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
pointd keys add $WALLET
```

To recover your wallet using seed phrase
```
pointd keys add $WALLET --recover
```

Show your wallet list
```
pointd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
POINT_WALLET_ADDRESS=$(pointd keys show $WALLET -a)
POINT_VALOPER_ADDRESS=$(pointd keys show $WALLET --bech val -a)
echo 'export POINT_WALLET_ADDRESS='${POINT_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export POINT_VALOPER_ADDRESS='${POINT_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with buy point token.
```
Buy point on MEXC
```

### Create validator

check your wallet balance:
```
pointd query bank balances $POINT_WALLET_ADDRESS
```
To create your validator run command below
```
pointd tx staking create-validator \
  --amount 1000000000000000000000apoint \
  --from $WALLET \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(pointd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $POINT_CHAIN_ID \
  --gas="400000" \
  --gas-prices="0.025apoint" \
```

### Check your validator key
```
[[ $(pointd q staking validator $POINT_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(pointd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
pointd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu pointd -o cat
```

Start service
```
sudo systemctl start pointd
```

Stop service
```
sudo systemctl stop pointd
```

Restart service
```
sudo systemctl restart pointd
```

### Node info
Synchronization info
```
pointd status 2>&1 | jq .SyncInfo
```

Validator info
```
pointd status 2>&1 | jq .ValidatorInfo
```

Node info
```
pointd status 2>&1 | jq .NodeInfo
```

Show node id
```
pointd tendermint show-node-id
```

### Wallet operations
List of wallets
```
pointd keys list
```

Recover wallet
```
pointd keys add $WALLET --recover
```

Delete wallet
```
pointd keys delete $WALLET
```

Get wallet balance
```
pointd query bank balances $POINT_WALLET_ADDRESS
```

Transfer funds
```
pointd tx bank send $POINT_WALLET_ADDRESS <TO_POINT_WALLET_ADDRESS> 100000000000000000000apoint
```

### Voting
```
pointd tx gov vote 1 yes --from $WALLET --chain-id=$POINT_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
pointd tx staking delegate $POINT_VALOPER_ADDRESS 100000000000000000000apoint --from=$WALLET --chain-id=$POINT_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
pointd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 100000000000000000000apoint --from=$WALLET --chain-id=$POINT_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
pointd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$POINT_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
pointd tx distribution withdraw-rewards $POINT_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$POINT_CHAIN_ID
```

### Validator management
Edit validator
```
pointd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$POINT_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
bcnad tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$POINT_CHAIN_ID \
  --gas=auto
```
