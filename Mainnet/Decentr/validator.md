Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
echo "export WALLET=wallet" >> $HOME/.bash_profile
source $HOME/.bash_profile

decentrd keys add $WALLET
```

To recover your wallet using seed phrase
```
decentrd keys add $WALLET --recover
```

Show your wallet list
```
decentrd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
DECENTR_WALLET_ADDRESS=$(decentrd keys show $WALLET -a)
DECENTR_VALOPER_ADDRESS=$(decentrd keys show $WALLET --bech val -a)
echo 'export DECENTR_WALLET_ADDRESS='${DECENTR_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export DECENTR_VALOPER_ADDRESS='${DECENTR_VALOPER_ADDRESS} >> $HOME/.bash_profile
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
decentrd query bank balances $DECENTR_WALLET_ADDRESS
```
To create your validator run command below
```
decentrd tx staking create-validator \
  --amount 100000udec \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(decentrd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $DECENTR_CHAIN_ID
```

### Check your validator key
```
[[ $(decentrd q staking validator $DECENTR_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(decentrd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
decentrd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu decentrd -o cat
```

Start service
```
sudo systemctl start decentrd
```

Stop service
```
sudo systemctl stop decentrd
```

Restart service
```
sudo systemctl restart decentrd
```

### Node info
Synchronization info
```
decentrd status 2>&1 | jq .SyncInfo
```

Validator info
```
decentrd status 2>&1 | jq .ValidatorInfo
```

Node info
```
decentrd status 2>&1 | jq .NodeInfo
```

Show node id
```
decentrd tendermint show-node-id
```

### Wallet operations
List of wallets
```
decentrd keys list
```

Recover wallet
```
decentrd keys add $WALLET --recover
```

Delete wallet
```
decentrd keys delete $WALLET
```

Get wallet balance
```
decentrd query bank balances $DECENTR_WALLET_ADDRESS
```

Transfer funds
```
decentrd tx bank send $DECENTR_WALLET_ADDRESS <TO_DECENTR_WALLET_ADDRESS> 100000udec
```

### Voting
```
decentrd tx gov vote 1 yes --from $WALLET --chain-id=$DECENTR_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
decentrd tx staking delegate $DECENTR_VALOPER_ADDRESS 100000udec --from=$WALLET --chain-id=$DECENTR_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
decentrd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 100000udec --from=$WALLET --chain-id=$DECENTR_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
decentrd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$DECENTR_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
decentrd tx distribution withdraw-rewards $DECENTR_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$DECENTR_CHAIN_ID
```

### Validator management
Edit validator
```
decentrd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$DECENTR_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
decentrd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$DECENTR_CHAIN_ID \
  --gas=auto
```
### remove node
```
sudo systemctl stop decentrd
sudo systemctl disable decentrd
sudo rm /etc/systemd/system/decentr* -rf
sudo rm $(which decentrd) -rf
sudo rm $HOME/.decentr* -rf
sudo rm $HOME/chain -rf
sed -i '/DECENTR_/d' ~/.bash_profile

```

