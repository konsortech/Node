Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
echo "export WALLET=wallet" >> $HOME/.bash_profile
source $HOME/.bash_profile

haqqd keys add $WALLET
```

To recover your wallet using seed phrase
```
haqqd keys add $WALLET --recover
```

Show your wallet list
```
haqqd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
HAQQ_WALLET_ADDRESS=$(haqqd keys show $WALLET -a)
HAQQ_VALOPER_ADDRESS=$(haqqd keys show $WALLET --bech val -a)
echo 'export HAQQ_WALLET_ADDRESS='${HAQQ_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export HAQQ_VALOPER_ADDRESS='${HAQQ_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with discord faucet.
```
N/A
```

### Create validator

check your wallet balance:
```
haqqd query bank balances $HAQQ_WALLET_ADDRESS
```
To create your validator run command below
```
haqqd tx staking create-validator \
  --amount 100000aISLM \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(haqqd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $HAQQ_CHAIN_ID
```

### Check your validator key
```
[[ $(haqqd q staking validator $HAQQ_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(haqqd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
haqqd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu haqqd -o cat
```

Start service
```
sudo systemctl start haqqd
```

Stop service
```
sudo systemctl stop haqqd
```

Restart service
```
sudo systemctl restart haqqd
```

### Node info
Synchronization info
```
haqqd status 2>&1 | jq .SyncInfo
```

Validator info
```
haqqd status 2>&1 | jq .ValidatorInfo
```

Node info
```
haqqd status 2>&1 | jq .NodeInfo
```

Show node id
```
haqqd tendermint show-node-id
```

### Wallet operations
List of wallets
```
haqqd keys list
```

Recover wallet
```
haqqd keys add $WALLET --recover
```

Delete wallet
```
haqqd keys delete $WALLET
```

Get wallet balance
```
haqqd query bank balances $HAQQ_WALLET_ADDRESS
```

Transfer funds
```
haqqd tx bank send $HAQQ_WALLET_ADDRESS <TO_HAQQ_WALLET_ADDRESS> 100000aISLM
```

### Voting
```
haqqd tx gov vote 1 yes --from $WALLET --chain-id=$HAQQ_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
haqqd tx staking delegate $HAQQ_VALOPER_ADDRESS 100000aISLM --from=$WALLET --chain-id=$HAQQ_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
haqqd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 100000aISLM --from=$WALLET --chain-id=$HAQQ_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
haqqd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$HAQQ_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
haqqd tx distribution withdraw-rewards $HAQQ_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$HAQQ_CHAIN_ID
```

### Validator management
Edit validator
```
haqqd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$HAQQ_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
haqqd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$HAQQ_CHAIN_ID \
  --gas=auto
```
