Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
defundd keys add $WALLET
```

To recover your wallet using seed phrase
```
defundd keys add $WALLET --recover
```

Show your wallet list
```
defundd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
DEFUND_WALLET_ADDRESS=$(defundd keys show $WALLET -a)
DEFUND_VALOPER_ADDRESS=$(defundd keys show $WALLET --bech val -a)
echo 'export DEFUND_WALLET_ADDRESS='${DEFUND_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export DEFUND_VALOPER_ADDRESS='${DEFUND_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet

```
N/A
```

### Create validator

check your wallet balance:
```
defundd query bank balances $DEFUND_WALLET_ADDRESS
```
To create your validator run command below
```
defundd tx staking create-validator \
  --amount 1000000ufetf \
  --from $WALLET \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(defundd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $DEFUND_CHAIN_ID
```

### Check your validator key
```
[[ $(defundd q staking validator $DEFUND_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(defundd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
defundd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu defundd -o cat
```

Start service
```
sudo systemctl start defundd
```

Stop service
```
sudo systemctl stop defundd
```

Restart service
```
sudo systemctl restart defundd
```

### Node info
Synchronization info
```
defundd status 2>&1 | jq .SyncInfo
```

Validator info
```
defundd status 2>&1 | jq .ValidatorInfo
```

Node info
```
defundd status 2>&1 | jq .NodeInfo
```

Show node id
```
defundd tendermint show-node-id
```

### Wallet operations
List of wallets
```
defundd keys list
```

Recover wallet
```
defundd keys add $WALLET --recover
```

Delete wallet
```
defundd keys delete $WALLET
```

Get wallet balance
```
defundd query bank balances $DEFUND_WALLET_ADDRESS
```

Transfer funds
```
defundd tx bank send $DEFUND_WALLET_ADDRESS <TO_DEFUND_WALLET_ADDRESS> 1000000ufetf
```

### Voting
```
defundd tx gov vote 1 yes --from $WALLET --chain-id=$DEFUND_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
defundd tx staking delegate $DEFUND_VALOPER_ADDRESS 1000000ufetf --from=$WALLET --chain-id=$DEFUND_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
defundd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000ufetf --from=$WALLET --chain-id=$DEFUND_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
defundd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$DEFUND_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
defundd tx distribution withdraw-rewards $DEFUND_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$DEFUND_CHAIN_ID
```

### Validator management
Edit validator
```
defundd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$DEFUND_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
defundd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$DEFUND_CHAIN_ID \
  --gas=auto
```
