Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
swisstronikd keys add $WALLET
```

To recover your wallet using seed phrase
```
swisstronikd keys add $WALLET --recover
```

Show your wallet list
```
swisstronikd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
SWISSTRONIK_WALLET_ADDRESS=$(swisstronikd keys show $WALLET -a)
SWISSTRONIK_VALOPER_ADDRESS=$(swisstronikd keys show $WALLET --bech val -a)
echo 'export SWISSTRONIK_WALLET_ADDRESS='${SWISSTRONIK_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export SWISSTRONIK_VALOPER_ADDRESS='${SWISSTRONIK_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet

```
https://faucet.testnet.swisstronik.com/
```

### Create validator

check your wallet balance:
```
swisstronikd query bank balances $SWISSTRONIK_WALLET_ADDRESS
```
To create your validator run command below
```
swisstronikd tx staking create-validator \
  --amount 1000000uswtr \
  --from $WALLET \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(swisstronikd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $SWISSTRONIK_CHAIN_ID \
  --gas-prices 7uswtr
```

### Check your validator key
```
[[ $(swisstronikd q staking validator $SWISSTRONIK_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(swisstronikd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
swisstronikd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu swisstronikd -o cat
```

Start service
```
sudo systemctl start swisstronikd
```

Stop service
```
sudo systemctl stop swisstronikd
```

Restart service
```
sudo systemctl restart swisstronikd
```

### Node info
Synchronization info
```
swisstronikd status 2>&1 | jq .SyncInfo
```

Validator info
```
swisstronikd status 2>&1 | jq .ValidatorInfo
```

Node info
```
swisstronikd status 2>&1 | jq .NodeInfo
```

Show node id
```
swisstronikd tendermint show-node-id
```

### Wallet operations
List of wallets
```
swisstronikd keys list
```

Recover wallet
```
swisstronikd keys add $WALLET --recover
```

Delete wallet
```
swisstronikd keys delete $WALLET
```

Get wallet balance
```
swisstronikd query bank balances $SWISSTRONIK_WALLET_ADDRESS
```

Transfer funds
```
swisstronikd tx bank send $SWISSTRONIK_WALLET_ADDRESS <TO_SWISSTRONIK_WALLET_ADDRESS> 1000000uswtr
```

### Voting
```
swisstronikd tx gov vote 1 yes --from $WALLET --chain-id=$SWISSTRONIK_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
swisstronikd tx staking delegate $SWISSTRONIK_VALOPER_ADDRESS 1000000uswtr --from=$WALLET --chain-id=$SWISSTRONIK_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
swisstronikd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000uswtr --from=$WALLET --chain-id=$SWISSTRONIK_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
swisstronikd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$SWISSTRONIK_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
swisstronikd tx distribution withdraw-rewards $SWISSTRONIK_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$SWISSTRONIK_CHAIN_ID
```

### Validator management
Edit validator
```
swisstronikd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$SWISSTRONIK_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
swisstronikd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$SWISSTRONIK_CHAIN_ID \
  --gas=auto
```
