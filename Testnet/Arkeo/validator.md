Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
arkeod keys add $WALLET
```

To recover your wallet using seed phrase
```
arkeod keys add $WALLET --recover
```

Show your wallet list
```
arkeod keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
ARKEO_WALLET_ADDRESS=$(arkeod keys show $WALLET -a)
ARKEO_VALOPER_ADDRESS=$(arkeod keys show $WALLET --bech val -a)
echo 'export ARKEO_WALLET_ADDRESS='${ARKEO_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export ARKEO_VALOPER_ADDRESS='${ARKEO_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
Go to discord channel and request for faucet
```
N/A
```

### Create validator

check your wallet balance:
```
arkeod query bank balances $ARKEO_WALLET_ADDRESS
```
To create your validator run command below
```
arkeod tx staking create-validator \
--amount=1000000uarkeo \
--pubkey=$(arkeod tendermint show-validator) \
--moniker=$NODENAME \
--identity=<your identity> \
--details="<Your details>" \
--chain-id=arkeo \
--commission-rate=0.10 \
--commission-max-rate=0.20 \
--commission-max-change-rate=0.01 \
--min-self-delegation=1 \
--from=$WALLET \
--gas-prices=0.1uarkeo \
--gas-adjustment=1.5 \
--gas=auto \
-y
```

### Check your validator key
```
[[ $(arkeod q staking validator $SELF_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(arkeod status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
arkeod q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu arkeod -o cat
```

Start service
```
sudo systemctl start arkeod
```

Stop service
```
sudo systemctl stop arkeod
```

Restart service
```
sudo systemctl restart arkeod
```

### Node info
Synchronization info
```
arkeod status 2>&1 | jq .SyncInfo
```

Validator info
```
arkeod status 2>&1 | jq .ValidatorInfo
```

Node info
```
arkeod status 2>&1 | jq .NodeInfo
```

Show node id
```
arkeod tendermint show-node-id
```

### Wallet operations
List of wallets
```
arkeod keys list
```

Recover wallet
```
arkeod keys add $WALLET --recover
```

Delete wallet
```
arkeod keys delete $WALLET
```

Get wallet balance
```
arkeod query bank balances $SELF_WALLET_ADDRESS
```

Transfer funds
```
arkeod tx bank send $SELF_WALLET_ADDRESS <TO_SELF_WALLET_ADDRESS> 1000000000uself
```

### Voting
```
arkeod tx gov vote 1 yes --from $WALLET --chain-id=$SELF_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
arkeod tx staking delegate $ARKEO_VALOPER_ADDRESS 1000000uarkeo --from=$WALLET --chain-id=$ARKEO_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
arkeod tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000uarkeo --from=$WALLET --chain-id=$ARKEO_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
arkeod tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$ARKEO_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
arkeod tx distribution withdraw-rewards $ARKEO_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$ARKEO_CHAIN_ID
```

### Validator management
Edit validator
```
arkeod tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$ARKEO_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
arkeod tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$ARKEO_CHAIN_ID \
  --gas=auto
```
