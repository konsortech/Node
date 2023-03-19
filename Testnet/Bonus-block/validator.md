Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
echo "export WALLET=wallet" >> $HOME/.bash_profile
source $HOME/.bash_profile

bonus-blockd keys add $WALLET
```

To recover your wallet using seed phrase
```
bonus-blockd keys add $WALLET --recover
```

Show your wallet list
```
bonus-blockd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
BONUSBLOCK_WALLET_ADDRESS=$(bonus-blockd keys show $WALLET -a)
BONUSBLOCK_VALOPER_ADDRESS=$(bonus-blockd keys show $WALLET --bech val -a)
echo 'export BONUSBLOCK_WALLET_ADDRESS='${BONUSBLOCK_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export BONUSBLOCK_VALOPER_ADDRESS='${BONUSBLOCK_VALOPER_ADDRESS} >> $HOME/.bash_profile
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
bonus-blockd query bank balances $BONUSBLOCK_WALLET_ADDRESS
```
To create your validator run command below
```
bonus-blockd tx staking create-validator \
  --amount 100000ubonus \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(bonus-blockd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $BONUSBLOCK_CHAIN_ID
```

### Check your validator key
```
[[ $(bonus-blockd q staking validator $BONUSBLOCK_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(bonus-blockd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
bonus-blockd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu bonus-blockd -o cat
```

Start service
```
sudo systemctl start bonus-blockd
```

Stop service
```
sudo systemctl stop bonus-blockd
```

Restart service
```
sudo systemctl restart bonus-blockd
```

### Node info
Synchronization info
```
bonus-blockd status 2>&1 | jq .SyncInfo
```

Validator info
```
bonus-blockd status 2>&1 | jq .ValidatorInfo
```

Node info
```
bonus-blockd status 2>&1 | jq .NodeInfo
```

Show node id
```
bonus-blockd tendermint show-node-id
```

### Wallet operations
List of wallets
```
bonus-blockd keys list
```

Recover wallet
```
bonus-blockd keys add $WALLET --recover
```

Delete wallet
```
bonus-blockd keys delete $WALLET
```

Get wallet balance
```
bonus-blockd query bank balances $BONUSBLOCK_WALLET_ADDRESS
```

Transfer funds
```
bonus-blockd tx bank send $BONUSBLOCK_WALLET_ADDRESS <TO_BONUSBLOCK_WALLET_ADDRESS> 100000ubonus
```

### Voting
```
bonus-blockd tx gov vote 1 yes --from $WALLET --chain-id=$BONUSBLOCK_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
bonus-blockd tx staking delegate $BONUSBLOCK_VALOPER_ADDRESS 100000ubonus --from=$WALLET --chain-id=$BONUSBLOCK_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
bonus-blockd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 100000ubonus --from=$WALLET --chain-id=$BONUSBLOCK_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
bonus-blockd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$BONUSBLOCK_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
bonus-blockd tx distribution withdraw-rewards $BONUSBLOCK_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$BONUSBLOCK_CHAIN_ID
```

### Validator management
Edit validator
```
bonus-blockd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$BONUSBLOCK_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
bonus-blockd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$BONUSBLOCK_CHAIN_ID \
  --gas=auto
```
### remove node
```
sudo systemctl stop bonus-blockd && \
sudo systemctl disable bonus-blockd && \
rm -rf /etc/systemd/system/bonus-blockd.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf BonusBlock-chain && \
rm -rf bonus.sh && \
rm -rf .bonusblock && \
rm -rf $(which bonus-blockd)

```