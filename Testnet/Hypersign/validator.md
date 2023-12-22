Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
hid-noded keys add $WALLET
```

To recover your wallet using seed phrase
```
hid-noded keys add $WALLET --recover
```

Show your wallet list
```
hid-noded keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
HYPERSIGN_WALLET_ADDRESS=$(hid-noded keys show $WALLET -a)
HYPERSIGN_VALOPER_ADDRESS=$(hid-noded keys show $WALLET --bech val -a)
echo 'export HYPERSIGN_WALLET_ADDRESS='${HYPERSIGN_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export HYPERSIGN_VALOPER_ADDRESS='${HYPERSIGN_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet

```
Go to #testnet-faucet on discord channel
```

### Create validator

check your wallet balance:
```
hid-noded query bank balances $HYPERSIGN_WALLET_ADDRESS
```
To create your validator run command below
```
hid-noded tx staking create-validator \
  --amount 1000000uhid \
  --from $WALLET \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(hid-noded tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $HYPERSIGN_CHAIN_ID
  --gas-adjustment=1.5 \
  --gas=auto \
```

### Check your validator key
```
[[ $(hid-noded q staking validator $HYPERSIGN_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(hid-noded status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
hid-noded q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu hid-noded -o cat
```

Start service
```
sudo systemctl start hid-noded
```

Stop service
```
sudo systemctl stop hid-noded
```

Restart service
```
sudo systemctl restart hid-noded
```

### Node info
Synchronization info
```
hid-noded status 2>&1 | jq .SyncInfo
```

Validator info
```
hid-noded status 2>&1 | jq .ValidatorInfo
```

Node info
```
hid-noded status 2>&1 | jq .NodeInfo
```

Show node id
```
hid-noded tendermint show-node-id
```

### Wallet operations
List of wallets
```
hid-noded keys list
```

Recover wallet
```
hid-noded keys add $WALLET --recover
```

Delete wallet
```
hid-noded keys delete $WALLET
```

Get wallet balance
```
hid-noded query bank balances $HYPERSIGN_WALLET_ADDRESS
```

Transfer funds
```
hid-noded tx bank send $HYPERSIGN_WALLET_ADDRESS <TO_HYPERSIGN_WALLET_ADDRESS> 1000000uhid
```

### Voting
```
hid-noded tx gov vote 1 yes --from $WALLET --chain-id=$HYPERSIGN_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
hid-noded tx staking delegate $HYPERSIGN_VALOPER_ADDRESS 1000000uhid --from=$WALLET --chain-id=$HYPERSIGN_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
hid-noded tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000uhid --from=$WALLET --chain-id=$HYPERSIGN_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
hid-noded tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$HYPERSIGN_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
hid-noded tx distribution withdraw-rewards $HYPERSIGN_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$HYPERSIGN_CHAIN_ID
```

### Validator management
Edit validator
```
hid-noded tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$HYPERSIGN_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
hid-noded tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$HYPERSIGN_CHAIN_ID \
  --gas=auto
```
