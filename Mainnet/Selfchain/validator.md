Guide for Validator CLI

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
seflchaind keys add $WALLET
```

To recover your wallet using seed phrase
```
seflchaind keys add $WALLET --recover
```

Show your wallet list
```
seflchaind keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
SELFCHAIN_WALLET_ADDRESS=$(seflchaind keys show $WALLET -a)
SELFCHAIN_VALOPER_ADDRESS=$(seflchaind keys show $WALLET --bech val -a)
echo 'export SELFCHAIN_WALLET_ADDRESS='${SELFCHAIN_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export SELFCHAIN_VALOPER_ADDRESS='${SELFCHAIN_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Create validator

check your wallet balance:
```
seflchaind query bank balances $SELFCHAIN_WALLET_ADDRESS
```
To create your validator run command below
```
selfchaind tx staking create-validator \
  --amount 1000000uself \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(selfchaind tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $SELF_CHAIN_ID
  --gas-adjustment 1.4 \
  --gas auto \
  --gas-prices 0.005uslf
```

### Check your validator key
```
[[ $(seflchaind q staking validator $SELFCHAIN_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(seflchaind status | jq -r .validator_info.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
seflchaind q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu seflchaind -o cat
```

Start service
```
sudo systemctl start seflchaind
```

Stop service
```
sudo systemctl stop seflchaind
```

Restart service
```
sudo systemctl restart seflchaind
```

### Node info
Synchronization info
```
seflchaind status 2>&1 | jq .sync_info
```

Validator info
```
seflchaind status 2>&1 | jq .validator_info
```

Node info
```
seflchaind status 2>&1 | jq .node_info
```

Show node id
```
seflchaind tendermint show-node-id
```

### Wallet operations
List of wallets
```
seflchaind keys list
```

Recover wallet
```
seflchaind keys add $WALLET --recover
```

Delete wallet
```
seflchaind keys delete $WALLET
```

Get wallet balance
```
seflchaind query bank balances $SELFCHAIN_WALLET_ADDRESS
```

Transfer funds
```
seflchaind tx bank send $SELFCHAIN_WALLET_ADDRESS <TO_SELFCHAIN_WALLET_ADDRESS> 1000000uself
```

### Voting
```
seflchaind tx gov vote 1 yes --from $WALLET --chain-id=$SELFCHAIN_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
seflchaind tx staking delegate $SELFCHAIN_VALOPER_ADDRESS 1000000uself --from=$WALLET --chain-id=$SELFCHAIN_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
seflchaind tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000uself --from=$WALLET --chain-id=$SELFCHAIN_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
seflchaind tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$SELFCHAIN_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
seflchaind tx distribution withdraw-rewards $SELFCHAIN_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$SELFCHAIN_CHAIN_ID
```

### Validator management
Edit validator
```
seflchaind tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$SELFCHAIN_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
seflchaind tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$SELFCHAIN_CHAIN_ID \
  --gas=auto
```
