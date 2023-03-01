Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
echo "export WALLET=wallet" >> $HOME/.bash_profile
source $HOME/.bash_profile

odind keys add $WALLET
```

To recover your wallet using seed phrase
```
odind keys add $WALLET --recover
```

Show your wallet list
```
odind keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
ODIN_WALLET_ADDRESS=$(odind keys show $WALLET -a)
ODIN_VALOPER_ADDRESS=$(odind keys show $WALLET --bech val -a)
echo 'export ODIN_WALLET_ADDRESS='${ODIN_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export ODIN_VALOPER_ADDRESS='${ODIN_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with discord faucet.
```
-
```

### Create validator

check your wallet balance:
```
odind query bank balances $ODIN_WALLET_ADDRESS
```
To create your validator run command below
```
odind tx staking create-validator \
odind tx staking create-validator \
  --from $WALLET \
  --amount="1000000loki" \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --pubkey=$(odind tendermint show-validator) \
  --moniker="$MONIKER" \
  --identity="<your_keybase>" \
  --details="<your_detail>" \
  --website="<your_web>" \
  --min-self-delegation "1" \
  --chain-id=$ODIN_CHAIN_ID \
  --node tcp://127.0.0.1:23657 \
  --fees 2500loki
```

### Check your validator key
```
[[ $(odind q staking validator $ODIN_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(odind status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
odind q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu odind -o cat
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
odind status 2>&1 | jq .SyncInfo
```

Validator info
```
odind status 2>&1 | jq .ValidatorInfo
```

Node info
```
odind status 2>&1 | jq .NodeInfo
```

Show node id
```
odind tendermint show-node-id
```

### Wallet operations
List of wallets
```
odind keys list
```

Recover wallet
```
odind keys add $WALLET --recover
```

Delete wallet
```
odind keys delete $WALLET
```

Get wallet balance
```
odind query bank balances $ODIN_WALLET_ADDRESS
```

Transfer funds
```
odind tx bank send $ODIN_WALLET_ADDRESS <TO_ODIN_WALLET_ADDRESS> 100000uebl
```

### Voting
```
odind tx gov vote 1 yes --from $WALLET --chain-id=$ODIN_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
odind tx staking delegate $ODIN_VALOPER_ADDRESS 100000uebl --from=$WALLET --chain-id=$ODIN_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
odind tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 100000uebl --from=$WALLET --chain-id=$ODIN_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
odind tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$ODIN_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
odind tx distribution withdraw-rewards $ODIN_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$ODIN_CHAIN_ID
```

### Validator management
Edit validator
```
odind tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$ODIN_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
odind tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$ODIN_CHAIN_ID \
  --gas=auto
```
