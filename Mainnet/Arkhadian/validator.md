Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
arkh keys add $WALLET
```

To recover your wallet using seed phrase
```
arkh keys add $WALLET --recover
```

Show your wallet list
```
arkh keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
ARKHADIAN_WALLET_ADDRESS=$(arkh keys show $WALLET -a)
ARKHADIAN_VALOPER_ADDRESS=$(arkh keys show $WALLET --bech val -a)
echo 'export ARKHADIAN_WALLET_ADDRESS='${ARKHADIAN_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export ARKHADIAN_VALOPER_ADDRESS='${ARKHADIAN_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet

```
N/A
```

### Create validator

check your wallet balance:
```
arkh query bank balances $ARKHADIAN_WALLET_ADDRESS
```
To create your validator run command below
```
arkh tx staking create-validator \
  --from $walletwallet \
  --amount="1000000arkh" \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --pubkey=$(arkh tendermint show-validator) \
  --moniker="$NODENAME" \
  --min-self-delegation "1" \
  --chain-id="arkh" \
  --node https://asc-dataseed.arkhadian.com:443 \
  --fees 250arkh
```

### Check your validator key
```
[[ $(arkh q staking validator $ARKHADIAN_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(arkh status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
arkh q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu arkh -o cat
```

Start service
```
sudo systemctl start arkh
```

Stop service
```
sudo systemctl stop arkh
```

Restart service
```
sudo systemctl restart arkh
```

### Node info
Synchronization info
```
arkh status 2>&1 | jq .SyncInfo
```

Validator info
```
arkh status 2>&1 | jq .ValidatorInfo
```

Node info
```
arkh status 2>&1 | jq .NodeInfo
```

Show node id
```
arkh tendermint show-node-id
```

### Wallet operations
List of wallets
```
arkh keys list
```

Recover wallet
```
arkh keys add $WALLET --recover
```

Delete wallet
```
arkh keys delete $WALLET
```

Get wallet balance
```
arkh query bank balances $ARKHADIAN_WALLET_ADDRESS
```

Transfer funds
```
arkh tx bank send $ARKHADIAN_WALLET_ADDRESS <TO_ARKHADIAN_WALLET_ADDRESS> 1000000arkh
```

### Voting
```
arkh tx gov vote 1 yes --from $WALLET --chain-id=$ARKHADIAN_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
arkh tx staking delegate $ARKHADIAN_VALOPER_ADDRESS 1000000arkh --from=$WALLET --chain-id=$ARKHADIAN_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
arkh tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000arkh --from=$WALLET --chain-id=$ARKHADIAN_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
arkh tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$ARKHADIAN_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
arkh tx distribution withdraw-rewards $ARKHADIAN_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$ARKHADIAN_CHAIN_ID
```

### Validator management
Edit validator
```
arkh tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$ARKHADIAN_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
arkh tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$ARKHADIAN_CHAIN_ID \
  --gas=auto
```
