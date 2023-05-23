Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
nolusd keys add $WALLET
```

To recover your wallet using seed phrase
```
nolusd keys add $WALLET --recover
```

Show your wallet list
```
nolusd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
NOLUS_WALLET_ADDRESS=$(nolusd keys show $WALLET -a)
NOLUS_VALOPER_ADDRESS=$(nolusd keys show $WALLET --bech val -a)
echo 'export NOLUS_WALLET_ADDRESS='${NOLUS_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export NOLUS_VALOPER_ADDRESS='${NOLUS_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
God to discord channel and request #testnet-faucet
```
N/A
```

### Create validator

check your wallet balance:
```
nolusd query bank balances $NOLUS_WALLET_ADDRESS
```
To create your validator run command below
```
nolusd tx staking create-validator \
  --amount 1000000unls \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(nolusd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $NOLUS_CHAIN_ID
  --gas-prices==0.1unls \
  --gas-adjustment=1.5 \
  --gas=auto \
```

### Check your validator key
```
[[ $(nolusd q staking validator $NOLUS_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(nolusd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
nolusd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu nolusd -o cat
```

Start service
```
sudo systemctl start nolusd
```

Stop service
```
sudo systemctl stop nolusd
```

Restart service
```
sudo systemctl restart nolusd
```

### Node info
Synchronization info
```
nolusd status 2>&1 | jq .SyncInfo
```

Validator info
```
nolusd status 2>&1 | jq .ValidatorInfo
```

Node info
```
nolusd status 2>&1 | jq .NodeInfo
```

Show node id
```
nolusd tendermint show-node-id
```

### Wallet operations
List of wallets
```
nolusd keys list
```

Recover wallet
```
nolusd keys add $WALLET --recover
```

Delete wallet
```
nolusd keys delete $WALLET
```

Get wallet balance
```
nolusd query bank balances $NOLUS_WALLET_ADDRESS
```

Transfer funds
```
nolusd tx bank send $NOLUS_WALLET_ADDRESS <TO_NOLUS_WALLET_ADDRESS> 1000000unls
```

### Voting
```
nolusd tx gov vote 1 yes --from $WALLET --chain-id=$NOLUS_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
nolusd tx staking delegate $NOLUS_VALOPER_ADDRESS 1000000unls --from=$WALLET --chain-id=$NOLUS_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
nolusd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000unls --from=$WALLET --chain-id=$NOLUS_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
nolusd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$NOLUS_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
nolusd tx distribution withdraw-rewards $NOLUS_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$NOLUS_CHAIN_ID
```

### Validator management
Edit validator
```
nolusd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$NOLUS_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
nolusd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$NOLUS_CHAIN_ID \
  --gas=auto
```
