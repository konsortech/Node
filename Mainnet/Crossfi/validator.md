## Guidance for Validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
crossfid keys add $WALLET
```

To recover your wallet using seed phrase
```
crossfid keys add $WALLET --recover
```

Show your wallet list
```
crossfid keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
CROSSFI_WALLET_ADDRESS=$(crossfid keys show $WALLET -a)
CROSSFI_VALOPER_ADDRESS=$(crossfid keys show $WALLET --bech val -a)
echo 'export CROSSFI_WALLET_ADDRESS='${CROSSFI_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export CROSSFI_VALOPER_ADDRESS='${CROSSFI_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet

```
N/A
```

### Create validator

check your wallet balance:
```
crossfid query bank balances $CROSSFI_WALLET_ADDRESS
```
To create your validator run command below
```
crossfid tx staking create-validator \
  --amount 1000000mpx \
  --from $WALLET \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(crossfid tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $CROSSFI_CHAIN_ID
  --gas auto \
  --gas-adjustment 1.5 \
  --gas-prices 10000000000000mpx
```

### Check your validator key
```
[[ $(crossfid q staking validator $CROSSFI_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(crossfid status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
crossfid q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu crossfid -o cat
```

Start service
```
sudo systemctl start crossfid
```

Stop service
```
sudo systemctl stop crossfid
```

Restart service
```
sudo systemctl restart crossfid
```

### Node info
Synchronization info
```
crossfid status 2>&1 | jq .SyncInfo
```

Validator info
```
crossfid status 2>&1 | jq .ValidatorInfo
```

Node info
```
crossfid status 2>&1 | jq .NodeInfo
```

Show node id
```
crossfid tendermint show-node-id
```

### Wallet operations
List of wallets
```
crossfid keys list
```

Recover wallet
```
crossfid keys add $WALLET --recover
```

Delete wallet
```
crossfid keys delete $WALLET
```

Get wallet balance
```
crossfid query bank balances $CROSSFI_WALLET_ADDRESS
```

Transfer funds
```
crossfid tx bank send $CROSSFI_WALLET_ADDRESS <TO_CROSSFI_WALLET_ADDRESS> 1000000mpx --gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx
```

### Voting
```
crossfid tx gov vote 1 yes --from $WALLET --chain-id=$CROSSFI_CHAIN_ID --gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx
```

### Staking, Delegation and Rewards
Delegate stake
```
crossfid tx staking delegate $CROSSFI_VALOPER_ADDRESS 1000000mpx --from=$WALLET --chain-id=$CROSSFI_CHAIN_ID --gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx
```

Redelegate stake from validator to another validator
```
crossfid tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000mpx --from=$WALLET --chain-id=$CROSSFI_CHAIN_ID --gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx
```

Withdraw all rewards
```
crossfid tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$CROSSFI_CHAIN_ID --gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx
```

Withdraw rewards with commision
```
crossfid tx distribution withdraw-rewards $CROSSFI_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$CROSSFI_CHAIN_ID --gas auto --gas-adjustment 1.5 --gas-prices 10000000000000mpx
```

### Validator management
Edit validator
```
crossfid tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$CROSSFI_CHAIN_ID \
  --from=$WALLET
  --gas auto \
  --gas-adjustment 1.5 \
  --gas-prices 10000000000000mpx
```

Unjail validator
```
crossfid tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$CROSSFI_CHAIN_ID \
  --gas auto \
  --gas-adjustment 1.5 \
  --gas-prices 10000000000000mpx
```
