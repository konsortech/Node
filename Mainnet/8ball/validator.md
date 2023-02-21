Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
echo "export WALLET=wallet" >> $HOME/.bash_profile
source $HOME/.bash_profile

8ball keys add $WALLET
```

To recover your wallet using seed phrase
```
8ball keys add $WALLET --recover
```

Show your wallet list
```
8ball keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
EIGHTBALL_WALLET_ADDRESS=$(8ball keys show $WALLET -a)
EIGHTBALL_VALOPER_ADDRESS=$(8ball keys show $WALLET --bech val -a)
echo 'export EIGHTBALL_WALLET_ADDRESS='${EIGHTBALL_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export EIGHTBALL_VALOPER_ADDRESS='${EIGHTBALL_VALOPER_ADDRESS} >> $HOME/.bash_profile
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
8ball query bank balances $EIGHTBALL_WALLET_ADDRESS
```
To create your validator run command below
```
8ball tx staking create-validator \
  --amount 100000uebl \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(8ball tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $EIGHTBALL_CHAIN_ID
```

### Check your validator key
```
[[ $(8ball q staking validator $EIGHTBALL_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(8ball status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
8ball q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu 8ball -o cat
```

Start service
```
sudo systemctl start 8ball
```

Stop service
```
sudo systemctl stop 8ball
```

Restart service
```
sudo systemctl restart 8ball
```

### Node info
Synchronization info
```
8ball status 2>&1 | jq .SyncInfo
```

Validator info
```
8ball status 2>&1 | jq .ValidatorInfo
```

Node info
```
8ball status 2>&1 | jq .NodeInfo
```

Show node id
```
8ball tendermint show-node-id
```

### Wallet operations
List of wallets
```
8ball keys list
```

Recover wallet
```
8ball keys add $WALLET --recover
```

Delete wallet
```
8ball keys delete $WALLET
```

Get wallet balance
```
8ball query bank balances $EIGHTBALL_WALLET_ADDRESS
```

Transfer funds
```
8ball tx bank send $EIGHTBALL_WALLET_ADDRESS <TO_EIGHTBALL_WALLET_ADDRESS> 100000uebl
```

### Voting
```
8ball tx gov vote 1 yes --from $WALLET --chain-id=$EIGHTBALL_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
8ball tx staking delegate $EIGHTBALL_VALOPER_ADDRESS 100000uebl --from=$WALLET --chain-id=$EIGHTBALL_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
8ball tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 100000uebl --from=$WALLET --chain-id=$EIGHTBALL_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
8ball tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$EIGHTBALL_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
8ball tx distribution withdraw-rewards $EIGHTBALL_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$EIGHTBALL_CHAIN_ID
```

### Validator management
Edit validator
```
8ball tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$EIGHTBALL_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
8ball tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$EIGHTBALL_CHAIN_ID \
  --gas=auto
```
