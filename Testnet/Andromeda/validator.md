Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
andromedad keys add $WALLET
```

To recover your wallet using seed phrase
```
andromedad keys add $WALLET --recover
```

Show your wallet list
```
andromedad keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
ANDROMEDA_WALLET_ADDRESS=$(andromedad keys show $WALLET -a)
ANDROMEDA_VALOPER_ADDRESS=$(andromedad keys show $WALLET --bech val -a)
echo 'export ANDROMEDA_WALLET_ADDRESS='${ANDROMEDA_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export ANDROMEDA_VALOPER_ADDRESS='${ANDROMEDA_VALOPER_ADDRESS} >> $HOME/.bash_profile
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
andromedad query bank balances $ANDROMEDA_WALLET_ADDRESS
```
To create your validator run command below
```
andromedad tx staking create-validator \
  --amount 100000uandr \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(andromedad tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $ANDROMEDA_CHAIN_ID
```

### Check your validator key
```
[[ $(andromedad q staking validator $ANDROMEDA_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(andromedad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
andromedad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu andromedad -o cat
```

Start service
```
sudo systemctl start andromedad
```

Stop service
```
sudo systemctl stop andromedad
```

Restart service
```
sudo systemctl restart andromedad
```

### Node info
Synchronization info
```
andromedad status 2>&1 | jq .SyncInfo
```

Validator info
```
andromedad status 2>&1 | jq .ValidatorInfo
```

Node info
```
andromedad status 2>&1 | jq .NodeInfo
```

Show node id
```
andromedad tendermint show-node-id
```

### Wallet operations
List of wallets
```
andromedad keys list
```

Recover wallet
```
andromedad keys add $WALLET --recover
```

Delete wallet
```
andromedad keys delete $WALLET
```

Get wallet balance
```
andromedad query bank balances $ANDROMEDA_WALLET_ADDRESS
```

Transfer funds
```
andromedad tx bank send $ANDROMEDA_WALLET_ADDRESS <TO_ANDROMEDA_WALLET_ADDRESS> 100000uandr
```

### Voting
```
andromedad tx gov vote 1 yes --from $WALLET --chain-id=$ANDROMEDA_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
andromedad tx staking delegate $ANDROMEDA_VALOPER_ADDRESS 100000uandr --from=$WALLET --chain-id=$ANDROMEDA_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
andromedad tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 100000uandr --from=$WALLET --chain-id=$ANDROMEDA_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
andromedad tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$ANDROMEDA_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
andromedad tx distribution withdraw-rewards $ANDROMEDA_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$ANDROMEDA_CHAIN_ID
```

### Validator management
Edit validator
```
andromedad tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$ANDROMEDA_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
andromedad tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$ANDROMEDA_CHAIN_ID \
  --gas=auto
```
