Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
echo "export WALLET=wallet" >> $HOME/.bash_profile
source $HOME/.bash_profile

kyved keys add $WALLET
```

To recover your wallet using seed phrase
```
kyved keys add $WALLET --recover
```

Show your wallet list
```
kyved keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
KYVE_WALLET_ADDRESS=$(kyved keys show $WALLET -a)
KYVE_VALOPER_ADDRESS=$(kyved keys show $WALLET --bech val -a)
echo 'export KYVE_WALLET_ADDRESS='${KYVE_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export KYVE_VALOPER_ADDRESS='${KYVE_VALOPER_ADDRESS} >> $HOME/.bash_profile
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
kyved query bank balances $KYVE_WALLET_ADDRESS
```
To create your validator run command below
```
kyved tx staking create-validator \
  --amount 100000ukyve \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(kyved tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $KYVE_CHAIN_ID
```

### Check your validator key
```
[[ $(kyved q staking validator $KYVE_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(kyved status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
kyved q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu kyved -o cat
```

Start service
```
sudo systemctl start kyved
```

Stop service
```
sudo systemctl stop kyved
```

Restart service
```
sudo systemctl restart kyved
```

### Node info
Synchronization info
```
kyved status 2>&1 | jq .SyncInfo
```

Validator info
```
kyved status 2>&1 | jq .ValidatorInfo
```

Node info
```
kyved status 2>&1 | jq .NodeInfo
```

Show node id
```
kyved tendermint show-node-id
```

### Wallet operations
List of wallets
```
kyved keys list
```

Recover wallet
```
kyved keys add $WALLET --recover
```

Delete wallet
```
kyved keys delete $WALLET
```

Get wallet balance
```
kyved query bank balances $KYVE_WALLET_ADDRESS
```

Transfer funds
```
kyved tx bank send $KYVE_WALLET_ADDRESS <TO_KYVE_WALLET_ADDRESS> 100000ukyve
```

### Voting
```
kyved tx gov vote 1 yes --from $WALLET --chain-id=$KYVE_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
kyved tx staking delegate $KYVE_VALOPER_ADDRESS 100000ukyve --from=$WALLET --chain-id=$KYVE_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
kyved tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 100000ukyve --from=$WALLET --chain-id=$KYVE_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
kyved tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$KYVE_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
kyved tx distribution withdraw-rewards $KYVE_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$KYVE_CHAIN_ID
```

### Validator management
Edit validator
```
kyved tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$KYVE_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
kyved tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$KYVE_CHAIN_ID \
  --gas=auto
```
### remove node
```
sudo systemctl stop kyved
sudo systemctl disable kyved
sudo rm /etc/systemd/system/kyve* -rf
sudo rm $(which kyved) -rf
sudo rm $HOME/.kyve* -rf
sudo rm $HOME/chain -rf
sed -i '/KYVE_/d' ~/.bash_profile

```