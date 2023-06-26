Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
timpid keys add $WALLET
```

To recover your wallet using seed phrase
```
timpid keys add $WALLET --recover
```

Show your wallet list
```
timpid keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
TIMPI_WALLET_ADDRESS=$(timpid keys show $WALLET -a)
TIMPI_VALOPER_ADDRESS=$(timpid keys show $WALLET --bech val -a)
echo 'export TIMPI_WALLET_ADDRESS='${TIMPI_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export TIMPI_VALOPER_ADDRESS='${TIMPI_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with discord faucet.
```
Go to http://173.249.54.208:1337/YOURWALLET
and replace with YOURWALLET
```

### Create validator

check your wallet balance:
```
timpid query bank balances $TIMPI_WALLET_ADDRESS
```
To create your validator run command below
```
timpid tx staking create-validator \
  --amount 1500000utimpiTN \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(timpid tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $TIMPI_CHAIN_ID \
  --gas="auto" \
  --gas-prices="0.0025utimpiTN" \
  --gas-adjustment="1.5"
```

### Check your validator key
```
[[ $(timpid q staking validator $TIMPI_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(timpid status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
timpid q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu timpid -o cat
```

Start service
```
sudo systemctl start timpid
```

Stop service
```
sudo systemctl stop timpid
```

Restart service
```
sudo systemctl restart timpid
```

### Node info
Synchronization info
```
timpid status 2>&1 | jq .SyncInfo
```

Validator info
```
timpid status 2>&1 | jq .ValidatorInfo
```

Node info
```
timpid status 2>&1 | jq .NodeInfo
```

Show node id
```
timpid tendermint show-node-id
```

### Wallet operations
List of wallets
```
timpid keys list
```

Recover wallet
```
timpid keys add $WALLET --recover
```

Delete wallet
```
timpid keys delete $WALLET
```

Get wallet balance
```
timpid query bank balances $TIMPI_WALLET_ADDRESS
```

Transfer funds
```
timpid tx bank send $TIMPI_WALLET_ADDRESS <TO_TIMPI_WALLET_ADDRESS> 1500000utimpiTN
```

### Voting
```
timpid tx gov vote 1 yes --from $WALLET --chain-id=$TIMPI_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
timpid tx staking delegate $TIMPI_VALOPER_ADDRESS 1500000utimpiTN --from=$WALLET --chain-id=$TIMPI_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
timpid tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1500000utimpiTN --from=$WALLET --chain-id=$TIMPI_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
timpid tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$TIMPI_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
timpid tx distribution withdraw-rewards $TIMPI_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$TIMPI_CHAIN_ID
```

### Validator management
Edit validator
```
timpid tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$TIMPI_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
timpid tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$TIMPI_CHAIN_ID \
  --gas=auto
```
