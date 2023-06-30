Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
empowerd keys add $WALLET
```

To recover your wallet using seed phrase
```
empowerd keys add $WALLET --recover
```

Show your wallet list
```
empowerd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
EMPOWER_WALLET_ADDRESS=$(empowerd keys show $WALLET -a)
EMPOWER_VALOPER_ADDRESS=$(empowerd keys show $WALLET --bech val -a)
echo 'export EMPOWER_WALLET_ADDRESS='${EMPOWER_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export EMPOWER_VALOPER_ADDRESS='${EMPOWER_VALOPER_ADDRESS} >> $HOME/.bash_profile
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
empowerd query bank balances $EMPOWER_WALLET_ADDRESS
```
To create your validator run command below
```
empowerd tx staking create-validator \
  --amount 1000000umpwr \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(empowerd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $EMPOWER_CHAIN_ID
```

### Check your validator key
```
[[ $(empowerd q staking validator $EMPOWER_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(empowerd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
empowerd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu empowerd -o cat
```

Start service
```
sudo systemctl start empowerd
```

Stop service
```
sudo systemctl stop empowerd
```

Restart service
```
sudo systemctl restart empowerd
```

### Node info
Synchronization info
```
empowerd status 2>&1 | jq .SyncInfo
```

Validator info
```
empowerd status 2>&1 | jq .ValidatorInfo
```

Node info
```
empowerd status 2>&1 | jq .NodeInfo
```

Show node id
```
empowerd tendermint show-node-id
```

### Wallet operations
List of wallets
```
empowerd keys list
```

Recover wallet
```
empowerd keys add $WALLET --recover
```

Delete wallet
```
empowerd keys delete $WALLET
```

Get wallet balance
```
empowerd query bank balances $EMPOWER_WALLET_ADDRESS
```

Transfer funds
```
empowerd tx bank send $EMPOWER_WALLET_ADDRESS <TO_EMPOWER_WALLET_ADDRESS> 1000000umpwr
```

### Voting
```
empowerd tx gov vote 1 yes --from $WALLET --chain-id=$EMPOWER_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
empowerd tx staking delegate $EMPOWER_VALOPER_ADDRESS 1000000umpwr --from=$WALLET --chain-id=$EMPOWER_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
empowerd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000umpwr --from=$WALLET --chain-id=$EMPOWER_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
empowerd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$EMPOWER_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
empowerd tx distribution withdraw-rewards $EMPOWER_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$EMPOWER_CHAIN_ID
```

### Validator management
Edit validator
```
empowerd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$EMPOWER_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
empowerd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$EMPOWER_CHAIN_ID \
  --gas=auto
```
