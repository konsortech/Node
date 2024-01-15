## CLI command

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
blockxd keys add $WALLET
```

To recover your wallet using seed phrase
```
blockxd keys add $WALLET --recover
```

Show your wallet list
```
blockxd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
BLOCKX_WALLET_ADDRESS=$(blockxd keys show $WALLET -a)
BLOCKX_VALOPER_ADDRESS=$(blockxd keys show $WALLET --bech val -a)
echo 'export BLOCKX_WALLET_ADDRESS='${BLOCKX_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export BLOCKX_VALOPER_ADDRESS='${BLOCKX_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Create validator

check your wallet balance:
```
blockxd query bank balances $BLOCKX_WALLET_ADDRESS
```
To create your validator run command below
```
blockxd tx staking create-validator \
  --amount 1000000abcx \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(blockxd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $BLOCKX_CHAIN_ID\
  --gas auto \
  --fees=2000abcx 
```

### Check your validator key
```
[[ $(blockxd q staking validator $BLOCKX_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(blockxd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
blockxd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu blockxd -o cat
```

Start service
```
sudo systemctl start blockxd
```

Stop service
```
sudo systemctl stop blockxd
```

Restart service
```
sudo systemctl restart blockxd
```

### Node info
Synchronization info
```
blockxd status 2>&1 | jq .SyncInfo
```

Validator info
```
blockxd status 2>&1 | jq .ValidatorInfo
```

Node info
```
blockxd status 2>&1 | jq .NodeInfo
```

Show node id
```
blockxd tendermint show-node-id
```

### Wallet operations
List of wallets
```
blockxd keys list
```

Recover wallet
```
blockxd keys add $WALLET --recover
```

Delete wallet
```
blockxd keys delete $WALLET
```

Get wallet balance
```
blockxd query bank balances $BLOCKX_WALLET_ADDRESS
```

Transfer funds
```
blockxd tx bank send $BLOCKX_WALLET_ADDRESS <TO_BLOCKX_WALLET_ADDRESS> 1000000abcx
```

### Voting
```
blockxd tx gov vote 1 yes --from $WALLET --chain-id=$BLOCKX_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
blockxd tx staking delegate $BLOCKX_VALOPER_ADDRESS 1000000abcx --from=$WALLET --chain-id=$BLOCKX_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
blockxd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000abcx --from=$WALLET --chain-id=$BLOCKX_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
blockxd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$BLOCKX_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
blockxd tx distribution withdraw-rewards $BLOCKX_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$BLOCKX_CHAIN_ID
```

### Validator management
Edit validator
```
blockxd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$BLOCKX_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
blockxd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$BLOCKX_CHAIN_ID \
  --gas=auto
```
