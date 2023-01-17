Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
planqd keys add $WALLET
```

To recover your wallet using seed phrase
```
planqd keys add $WALLET --recover
```

Show your wallet list
```
planqd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
PLANQ_WALLET_ADDRESS=$(planqd keys show $WALLET -a)
PLANQ_VALOPER_ADDRESS=$(planqd keys show $WALLET --bech val -a)
echo 'export PLANQ_WALLET_ADDRESS='${PLANQ_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export PLANQ_VALOPER_ADDRESS='${PLANQ_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with buy planq token.
```
https://docs.planq.network/about/airdrop.html
```

### Create validator

check your wallet balance:
```
planqd query bank balances $PLANQ_WALLET_ADDRESS
```
To create your validator run command below
```
planqd tx staking create-validator \
  --amount 1000000000000aplanq \
  --from $WALLET \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(planqd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $PLANQ_CHAIN_ID \
  --gas="1000000" \
  --gas-prices="30000000000aplanq" \
  --gas-adjustment="1.15"
```

### Check your validator key
```
[[ $(planqd q staking validator $PLANQ_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(planqd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
planqd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu planqd -o cat
```

Start service
```
sudo systemctl start planqd
```

Stop service
```
sudo systemctl stop planqd
```

Restart service
```
sudo systemctl restart planqd
```

### Node info
Synchronization info
```
planqd status 2>&1 | jq .SyncInfo
```

Validator info
```
planqd status 2>&1 | jq .ValidatorInfo
```

Node info
```
planqd status 2>&1 | jq .NodeInfo
```

Show node id
```
planqd tendermint show-node-id
```

### Wallet operations
List of wallets
```
planqd keys list
```

Recover wallet
```
planqd keys add $WALLET --recover
```

Delete wallet
```
planqd keys delete $WALLET
```

Get wallet balance
```
planqd query bank balances $PLANQ_WALLET_ADDRESS
```

Transfer funds
```
planqd tx bank send $PLANQ_WALLET_ADDRESS <TO_PLANQ_WALLET_ADDRESS> 10000aplanq
```

### Voting
```
planqd tx gov vote 1 yes --from $WALLET --chain-id=$PLANQ_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
planqd tx staking delegate $PLANQ_VALOPER_ADDRESS 1000000000000aplanq --from=$WALLET --chain-id=$PLANQ_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
planqd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000000000aplanq --from=$WALLET --chain-id=$PLANQ_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
planqd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$PLANQ_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
planqd tx distribution withdraw-rewards $PLANQ_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$PLANQ_CHAIN_ID
```

### Validator management
Edit validator
```
planqd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$PLANQ_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
bcnad tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$PLANQ_CHAIN_ID \
  --gas=auto
```
