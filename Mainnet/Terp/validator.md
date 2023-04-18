Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
terpd keys add $WALLET
```

To recover your wallet using seed phrase
```
terpd keys add $WALLET --recover
```

Show your wallet list
```
terpd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
TERP_WALLET_ADDRESS=$(terpd keys show $WALLET -a)
TERP_VALOPER_ADDRESS=$(terpd keys show $WALLET --bech val -a)
echo 'export TERP_WALLET_ADDRESS='${TERP_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export TERP_VALOPER_ADDRESS='${TERP_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet.
```
N/A
```

### Create validator

check your wallet balance:
```
terpd query bank balances $TERP_WALLET_ADDRESS
```
To create your validator run command below
```
terpd tx staking create-validator \
  --amount 1000000uterp \
  --from $WALLET \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(terpd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $TERP_CHAIN_ID \
  --gas auto \
  --gas-adjustment 1.3 \
  --fees 70000uthiol
```

### Check your validator key
```
[[ $(terpd q staking validator $TERP_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(terpd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
terpd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu terpd -o cat
```

Start service
```
sudo systemctl start terpd
```

Stop service
```
sudo systemctl stop terpd
```

Restart service
```
sudo systemctl restart terpd
```

### Node info
Synchronization info
```
terpd status 2>&1 | jq .SyncInfo
```

Validator info
```
terpd status 2>&1 | jq .ValidatorInfo
```

Node info
```
terpd status 2>&1 | jq .NodeInfo
```

Show node id
```
terpd tendermint show-node-id
```

### Wallet operations
List of wallets
```
terpd keys list
```

Recover wallet
```
terpd keys add $WALLET --recover
```

Delete wallet
```
terpd keys delete $WALLET
```

Get wallet balance
```
terpd query bank balances $TERP_WALLET_ADDRESS
```

Transfer funds
```
terpd tx bank send $TERP_WALLET_ADDRESS <TO_TERP_WALLET_ADDRESS> 1000000uterp
```

### Voting
```
terpd tx gov vote 1 yes --from $WALLET --chain-id=$TERP_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
terpd tx staking delegate $TERP_VALOPER_ADDRESS 1000000uterp --from=$WALLET --chain-id=$TERP_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
terpd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000uterp --from=$WALLET --chain-id=$TERP_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
terpd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$TERP_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
terpd tx distribution withdraw-rewards $TERP_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$TERP_CHAIN_ID
```

### Validator management
Edit validator
```
terpd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$TERP_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
bcnad tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$TERP_CHAIN_ID \
  --gas=auto
```
