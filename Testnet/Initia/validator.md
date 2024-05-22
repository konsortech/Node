Guide for Validator CLI

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
initiad keys add $WALLET
```

To recover your wallet using seed phrase
```
initiad keys add $WALLET --recover
```

Show your wallet list
```
initiad keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
INITIA_WALLET_ADDRESS=$(initiad keys show $WALLET -a)
INITIA_VALOPER_ADDRESS=$(initiad keys show $WALLET --bech val -a)
echo 'export INITIA_WALLET_ADDRESS='${INITIA_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export INITIA_VALOPER_ADDRESS='${INITIA_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
Faucet available on initia discord
```
go to #faucet-verification
```

### Create validator

To create your validator run command below
```
initiad tx mstaking create-validator \
--amount 1000000uinit \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(initiad tendermint show-validator) \
--moniker "$NODENAME" \
--identity "" \
--details "" \
--chain-id $INITIA_CHAIN_ID \
--gas auto \
--fees 80000uinit \
-y 
```

### Check your validator key
```
[[ $(initiad q staking validator $INITIA_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(initiad status | jq -r .validator_info.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
initiad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu initiad -o cat
```

Start service
```
sudo systemctl start initiad
```

Stop service
```
sudo systemctl stop initiad
```

Restart service
```
sudo systemctl restart initiad
```

### Node info
Synchronization info
```
initiad status 2>&1 | jq .sync_info
```

Validator info
```
initiad status 2>&1 | jq .validator_info
```

Node info
```
initiad status 2>&1 | jq .node_info
```

Show node id
```
initiad tendermint show-node-id
```

### Wallet operations
List of wallets
```
initiad keys list
```

Recover wallet
```
initiad keys add $WALLET --recover
```

Delete wallet
```
initiad keys delete $WALLET
```

Get wallet balance
```
initiad query bank balances $INITIA_WALLET_ADDRESS
```

Transfer funds
```
initiad tx bank send $INITIA_WALLET_ADDRESS <TO_INITIA_WALLET_ADDRESS> 1000000uinit
```

### Voting
```
initiad tx gov vote 1 yes --from $WALLET --chain-id=$INITIA_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
initiad tx staking delegate $INITIA_VALOPER_ADDRESS 1000000uinit --from=$WALLET --chain-id=$INITIA_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
initiad tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000uinit --from=$WALLET --chain-id=$INITIA_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
initiad tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$INITIA_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
initiad tx distribution withdraw-rewards $INITIA_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$INITIA_CHAIN_ID
```

### Validator management
Edit validator
```
initiad tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$INITIA_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
initiad tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$INITIA_CHAIN_ID \
  --gas=auto
```
