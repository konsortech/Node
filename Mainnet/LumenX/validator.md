Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
lumenxd keys add $WALLET
```

To recover your wallet using seed phrase
```
lumenxd keys add $WALLET --recover
```

Show your wallet list
```
lumenxd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
LUMENX_WALLET_ADDRESS=$(lumenxd keys show $WALLET -a)
LUMENX_VALOPER_ADDRESS=$(lumenxd keys show $WALLET --bech val -a)
echo 'export LUMENX_WALLET_ADDRESS='${LUMENX_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export LUMENX_VALOPER_ADDRESS='${LUMENX_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet

```
https://frontier.osmosis.zone/pool/788
```

### Create validator

check your wallet balance:
```
lumenxd query bank balances $LUMENX_WALLET_ADDRESS
```
To create your validator run command below
```
lumenxd tx staking create-validator \
  --amount 45000000000ulumen \
  --from $WALLET \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(lumenxd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $LUMENX_CHAIN_ID
  --gas auto \
  --gas-adjustment="1.15" \
  --gas-prices="0.0025ulumen"
```

### Check your validator key
```
[[ $(lumenxd q staking validator $LUMENX_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(lumenxd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
lumenxd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu lumenxd -o cat
```

Start service
```
sudo systemctl start lumenxd
```

Stop service
```
sudo systemctl stop lumenxd
```

Restart service
```
sudo systemctl restart lumenxd
```

### Node info
Synchronization info
```
lumenxd status 2>&1 | jq .SyncInfo
```

Validator info
```
lumenxd status 2>&1 | jq .ValidatorInfo
```

Node info
```
lumenxd status 2>&1 | jq .NodeInfo
```

Show node id
```
lumenxd tendermint show-node-id
```

### Wallet operations
List of wallets
```
lumenxd keys list
```

Recover wallet
```
lumenxd keys add $WALLET --recover
```

Delete wallet
```
lumenxd keys delete $WALLET
```

Get wallet balance
```
lumenxd query bank balances $LUMENX_WALLET_ADDRESS
```

Transfer funds
```
lumenxd tx bank send $LUMENX_WALLET_ADDRESS <TO_LUMENX_WALLET_ADDRESS> 45000000000ulumen
```

### Voting
```
lumenxd tx gov vote 1 yes --from $WALLET --chain-id=$LUMENX_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
lumenxd tx staking delegate $LUMENX_VALOPER_ADDRESS 45000000000ulumen --from=$WALLET --chain-id=$LUMENX_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
lumenxd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 45000000000ulumen --from=$WALLET --chain-id=$LUMENX_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
lumenxd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$LUMENX_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
lumenxd tx distribution withdraw-rewards $LUMENX_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$LUMENX_CHAIN_ID
```

### Validator management
Edit validator
```
lumenxd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$LUMENX_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
lumenxd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$LUMENX_CHAIN_ID \
  --gas=auto
```
