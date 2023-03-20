Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
aurad keys add $WALLET
```

To recover your wallet using seed phrase
```
aurad keys add $WALLET --recover
```

Show your wallet list
```
aurad keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
AURA_WALLET_ADDRESS=$(aurad keys show $WALLET -a)
AURA_VALOPER_ADDRESS=$(aurad keys show $WALLET --bech val -a)
echo 'export AURA_WALLET_ADDRESS='${AURA_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export AURA_VALOPER_ADDRESS='${AURA_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with discord faucet.
```
Go to Discord https://discord.gg/p3e8Zc6x
channel #euphoria-faucet
```

### Create validator

check your wallet balance:
```
aurad query bank balances $AURA_WALLET_ADDRESS
```
To create your validator run command below
```
aurad tx staking create-validator \
  --amount 100000uaura \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(aurad tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $AURA_CHAIN_ID
```

### Check your validator key
```
[[ $(aurad q staking validator $AURA_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(aurad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
aurad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu aurad -o cat
```

Start service
```
sudo systemctl start aurad
```

Stop service
```
sudo systemctl stop aurad
```

Restart service
```
sudo systemctl restart aurad
```

### Node info
Synchronization info
```
aurad status 2>&1 | jq .SyncInfo
```

Validator info
```
aurad status 2>&1 | jq .ValidatorInfo
```

Node info
```
aurad status 2>&1 | jq .NodeInfo
```

Show node id
```
aurad tendermint show-node-id
```

### Wallet operations
List of wallets
```
aurad keys list
```

Recover wallet
```
aurad keys add $WALLET --recover
```

Delete wallet
```
aurad keys delete $WALLET
```

Get wallet balance
```
aurad query bank balances $AURA_WALLET_ADDRESS
```

Transfer funds
```
aurad tx bank send $AURA_WALLET_ADDRESS <TO_AURA_WALLET_ADDRESS> 100000uaura
```

### Voting
```
aurad tx gov vote 1 yes --from $WALLET --chain-id=$AURA_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
aurad tx staking delegate $AURA_VALOPER_ADDRESS 100000uaura --from=$WALLET --chain-id=$AURA_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
aurad tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 100000uaura --from=$WALLET --chain-id=$AURA_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
aurad tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$AURA_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
aurad tx distribution withdraw-rewards $AURA_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$AURA_CHAIN_ID
```

### Validator management
Edit validator
```
aurad tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$AURA_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
aurad tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$AURA_CHAIN_ID \
  --gas=auto
```
