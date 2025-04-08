Guide for Node CLI

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
exrpd keys add $WALLET
```

To recover your wallet using seed phrase
```
exrpd keys add $WALLET --recover
```

Show your wallet list
```
exrpd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
XRPL_WALLET_ADDRESS=$(exrpd keys show $WALLET -a)
XRPL_VALOPER_ADDRESS=$(exrpd keys show $WALLET --bech val -a)
echo 'export XRPL_WALLET_ADDRESS='${XRPL_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export XRPL_VALOPER_ADDRESS='${XRPL_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Create validator

Follow this guid to join testnet validator:
```
https://docs.xrplevm.org/pages/operators/validators/join-the-proof-of-authority
```


### Check your validator key
```
[[ $(exrpd q staking validator $XRPL_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(exrpd status | jq -r .validator_info.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
exrpd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu exrpd -o cat
```

Start service
```
sudo systemctl start exrpd
```

Stop service
```
sudo systemctl stop exrpd
```

Restart service
```
sudo systemctl restart exrpd
```

### Node info
Synchronization info
```
exrpd status 2>&1 | jq .sync_info
```

Validator info
```
exrpd status 2>&1 | jq .validator_info
```

Node info
```
exrpd status 2>&1 | jq .node_info
```

Show node id
```
exrpd tendermint show-node-id
```

### Wallet operations
List of wallets
```
exrpd keys list
```

Recover wallet
```
exrpd keys add $WALLET --recover
```

Delete wallet
```
exrpd keys delete $WALLET
```

Get wallet balance
```
exrpd query bank balances $XRPL_WALLET_ADDRESS
```

Transfer funds
```
exrpd tx bank send $XRPL_WALLET_ADDRESS <TO_XRPL_WALLET_ADDRESS>
```

### Voting
```
exrpd tx gov vote 1 yes --from $WALLET --chain-id=$XRPL_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
exrpd tx staking delegate $XRPL_VALOPER_ADDRESS --from=$WALLET --chain-id=$XRPL_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
exrpd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> --from=$WALLET --chain-id=$XRPL_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
exrpd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$XRPL_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
exrpd tx distribution withdraw-rewards $XRPL_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$XRPL_CHAIN_ID
```

### Validator management
Edit validator
```
exrpd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$XRPL_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
exrpd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$XRPL_CHAIN_ID \
  --gas=auto
```
