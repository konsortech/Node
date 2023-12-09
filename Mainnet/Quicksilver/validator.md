## Guidence for Create Validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
quicksilverd keys add $WALLET
```

To recover your wallet using seed phrase
```
quicksilverd keys add $WALLET --recover
```

Show your wallet list
```
quicksilverd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
QUICKSILVER_WALLET_ADDRESS=$(quicksilverd keys show $WALLET -a)
QUICKSILVER_VALOPER_ADDRESS=$(quicksilverd keys show $WALLET --bech val -a)
echo 'export QUICKSILVER_WALLET_ADDRESS='${QUICKSILVER_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export QUICKSILVER_VALOPER_ADDRESS='${QUICKSILVER_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with discord faucet.

Buy on [Osmosis](https://app.osmosis.zone/?from=QCK&to=USDC.axl)
---

### Create validator

check your wallet balance:
```
quicksilverd query bank balances $QUICKSILVER_WALLET_ADDRESS
```
To create your validator run command below
```
quicksilverd tx staking create-validator \
  --amount 1000000uqck \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(quicksilverd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $QUICKSILVER_CHAIN_ID
  --gas-adjustment 1.4 \
  --gas auto \
  --gas-prices 0.0001uqck \
  -y
```

### Check your validator key
```
[[ $(quicksilverd q staking validator $QUICKSILVER_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(quicksilverd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
quicksilverd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu quicksilverd -o cat
```

Start service
```
sudo systemctl start quicksilverd
```

Stop service
```
sudo systemctl stop quicksilverd
```

Restart service
```
sudo systemctl restart quicksilverd
```

### Node info
Synchronization info
```
quicksilverd status 2>&1 | jq .SyncInfo
```

Validator info
```
quicksilverd status 2>&1 | jq .ValidatorInfo
```

Node info
```
quicksilverd status 2>&1 | jq .NodeInfo
```

Show node id
```
quicksilverd tendermint show-node-id
```

### Wallet operations
List of wallets
```
quicksilverd keys list
```

Recover wallet
```
quicksilverd keys add $WALLET --recover
```

Delete wallet
```
quicksilverd keys delete $WALLET
```

Get wallet balance
```
quicksilverd query bank balances $QUICKSILVER_WALLET_ADDRESS
```

Transfer funds
```
quicksilverd tx bank send $QUICKSILVER_WALLET_ADDRESS <TO_QUICKSILVER_WALLET_ADDRESS> 1000000uqck
```

### Voting
```
quicksilverd tx gov vote 1 yes --from $WALLET --chain-id=$QUICKSILVER_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
quicksilverd tx staking delegate $QUICKSILVER_VALOPER_ADDRESS 1000000uqck --from=$WALLET --chain-id=$QUICKSILVER_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
quicksilverd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000uqck --from=$WALLET --chain-id=$QUICKSILVER_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
quicksilverd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$QUICKSILVER_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
quicksilverd tx distribution withdraw-rewards $QUICKSILVER_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$QUICKSILVER_CHAIN_ID
```

### Validator management
Edit validator
```
quicksilverd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$QUICKSILVER_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
quicksilverd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$QUICKSILVER_CHAIN_ID \
  --gas=auto
```
