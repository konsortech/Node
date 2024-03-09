## Guidance for Validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
hedged keys add $WALLET
```

To recover your wallet using seed phrase
```
hedged keys add $WALLET --recover
```

Show your wallet list
```
hedged keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
HEDGE_WALLET_ADDRESS=$(hedged keys show $WALLET -a)
HEDGE_VALOPER_ADDRESS=$(hedged keys show $WALLET --bech val -a)
echo 'export HEDGE_WALLET_ADDRESS='${HEDGE_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export HEDGE_VALOPER_ADDRESS='${HEDGE_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Create validator

check your wallet balance:
```
hedged query bank balances $HEDGE_WALLET_ADDRESS
```
To create your validator run command below
```
hedged tx staking create-validator \
  --amount 1000000000uhedge \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey $(hedged tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $HEDGE_CHAIN_ID \
  --gas=auto \
  --gas-adjustment=1.5 \
  --gas-prices="0.025uhedge"
```

### Check your validator key
```
[[ $(hedged q staking validator $HEDGE_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(hedged status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
hedged q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu hedged -o cat
```

Start service
```
sudo systemctl start hedged
```

Stop service
```
sudo systemctl stop hedged
```

Restart service
```
sudo systemctl restart hedged
```

### Node info
Synchronization info
```
hedged status 2>&1 | jq .SyncInfo
```

Validator info
```
hedged status 2>&1 | jq .ValidatorInfo
```

Node info
```
hedged status 2>&1 | jq .NodeInfo
```

Show node id
```
hedged tendermint show-node-id
```

### Wallet operations
List of wallets
```
hedged keys list
```

Recover wallet
```
hedged keys add $WALLET --recover
```

Delete wallet
```
hedged keys delete $WALLET
```

Get wallet balance
```
hedged query bank balances $HEDGE_WALLET_ADDRESS
```

Transfer funds
```
hedged tx bank send $HEDGE_WALLET_ADDRESS <TO_HEDGE_WALLET_ADDRESS> 1000000uhedge
```

### Voting
```
hedged tx gov vote 1 yes --from $WALLET --chain-id=$HEDGE_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
hedged tx staking delegate $HEDGE_VALOPER_ADDRESS 1000000uhedge --from=$WALLET --chain-id=$HEDGE_CHAIN_ID --gas=”auto" --gas-prices=”0.025uhedge” --gas-adjustment=”1.5″
```

Redelegate stake from validator to another validator
```
hedged tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000uhedge --from=$WALLET --chain-id=$HEDGE_CHAIN_ID --gas=”auto" --gas-prices=”0.025uhedge” --gas-adjustment=”1.5″
```

Withdraw all rewards
```
hedged tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$HEDGE_CHAIN_ID --gas=”auto" --gas-prices=”0.025uhedge” --gas-adjustment=”1.5″
```

Withdraw rewards with commision
```
hedged tx distribution withdraw-rewards $HEDGE_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$HEDGE_CHAIN_ID --gas=”auto" --gas-prices=”0.025uhedge” --gas-adjustment=”1.5″
```

### Validator management
Edit validator
```
hedged tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$HEDGE_CHAIN_ID \
  --from=$WALLET \
  --gas=”auto" \
  --gas-prices=”0.025uhedge” \
  --gas-adjustment=”1.5″
```

Unjail validator
```
hedged tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$HEDGE_CHAIN_ID \
  --gas=”auto" \
  --gas-prices=”0.025uhedge” \
  --gas-adjustment=”1.5″
```
