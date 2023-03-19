Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
echo "export WALLET=wallet" >> $HOME/.bash_profile
source $HOME/.bash_profile

oraid keys add $WALLET
```

To recover your wallet using seed phrase
```
oraid keys add $WALLET --recover
```

Show your wallet list
```
oraid keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
ORAI_WALLET_ADDRESS=$(oraid keys show $WALLET -a)
ORAI_VALOPER_ADDRESS=$(oraid keys show $WALLET --bech val -a)
echo 'export ORAI_WALLET_ADDRESS='${ORAI_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export ORAI_VALOPER_ADDRESS='${ORAI_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with discord faucet.
```
n/a
```

### Create validator

check your wallet balance:
```
oraid query bank balances $ORAI_WALLET_ADDRESS
```
To create your validator run command below
```
oraid tx staking create-validator \
  --amount 100000orai \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(oraid tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $ORAI_CHAIN_ID
```

### Check your validator key
```
[[ $(oraid q staking validator $ORAI_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(oraid status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
oraid q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu oraid -o cat
```

Start service
```
sudo systemctl start oraid
```

Stop service
```
sudo systemctl stop oraid
```

Restart service
```
sudo systemctl restart oraid
```

### Node info
Synchronization info
```
oraid status 2>&1 | jq .SyncInfo
```

Validator info
```
oraid status 2>&1 | jq .ValidatorInfo
```

Node info
```
oraid status 2>&1 | jq .NodeInfo
```

Show node id
```
oraid tendermint show-node-id
```

### Wallet operations
List of wallets
```
oraid keys list
```

Recover wallet
```
oraid keys add $WALLET --recover
```

Delete wallet
```
oraid keys delete $WALLET
```

Get wallet balance
```
oraid query bank balances $ORAI_WALLET_ADDRESS
```

Transfer funds
```
oraid tx bank send $ORAI_WALLET_ADDRESS <TO_ORAI_WALLET_ADDRESS> 100000orai
```

### Voting
```
oraid tx gov vote 1 yes --from $WALLET --chain-id=$ORAI_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
oraid tx staking delegate $ORAI_VALOPER_ADDRESS 100000orai --from=$WALLET --chain-id=$ORAI_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
oraid tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 100000orai --from=$WALLET --chain-id=$ORAI_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
oraid tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$ORAI_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
oraid tx distribution withdraw-rewards $ORAI_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$ORAI_CHAIN_ID
```

### Validator management
Edit validator
```
oraid tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$ORAI_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
oraid tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$ORAI_CHAIN_ID \
  --gas=auto
```
### remove node
```
sudo systemctl stop oraid
sudo systemctl disable oraid
sudo rm /etc/systemd/system/oraid* -rf
sudo rm $(which oraid) -rf
sudo rm $HOME/.oraid* -rf
sudo rm $HOME/orai -rf
sed -i '/ORAI_/d' ~/.bash_profile

```

