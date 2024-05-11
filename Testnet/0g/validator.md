Guide for Validator CLI

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
0gchaind keys add $WALLET
```

To recover your wallet using seed phrase
```
0gchaind keys add $WALLET --recover
```

Show your wallet list
```
0gchaind keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
0G_WALLET_ADDRESS=$(0gchaind keys show $WALLET -a)
0G_VALOPER_ADDRESS=$(0gchaind keys show $WALLET --bech val -a)
echo 'export 0G_WALLET_ADDRESS='${0G_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export 0G_VALOPER_ADDRESS='${0G_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
Faucet available on website
```
https://faucet.0g.ai/
```

### Create validator

check your wallet balance:
```
0gchaind query bank balances $0G_WALLET_ADDRESS
```
To create your validator run command below
```
0gchaind tx staking create-validator \
  --amount 1000000ua0gi \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey $(0gchaind tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $0G_CHAIN_ID
  --gas-prices=0.25ua0gi  \
  --gas-adjustment=1.5 \
  --gas=auto 
```

### Check your validator key
```
[[ $(0gchaind q staking validator $0G_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(0gchaind status | jq -r .validator_info.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
0gchaind q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu 0gchaind -o cat
```

Start service
```
sudo systemctl start 0gchaind
```

Stop service
```
sudo systemctl stop 0gchaind
```

Restart service
```
sudo systemctl restart 0gchaind
```

### Node info
Synchronization info
```
0gchaind status 2>&1 | jq .sync_info
```

Validator info
```
0gchaind status 2>&1 | jq .validator_info
```

Node info
```
0gchaind status 2>&1 | jq .node_info
```

Show node id
```
0gchaind tendermint show-node-id
```

### Wallet operations
List of wallets
```
0gchaind keys list
```

Recover wallet
```
0gchaind keys add $WALLET --recover
```

Delete wallet
```
0gchaind keys delete $WALLET
```

Get wallet balance
```
0gchaind query bank balances $0G_WALLET_ADDRESS
```

Transfer funds
```
0gchaind tx bank send $0G_WALLET_ADDRESS <TO_0G_WALLET_ADDRESS> 1000000ua0gi
```

### Voting
```
0gchaind tx gov vote 1 yes --from $WALLET --chain-id=$0G_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
0gchaind tx staking delegate $0G_VALOPER_ADDRESS 1000000ua0gi --from=$WALLET --chain-id=$0G_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
0gchaind tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000ua0gi --from=$WALLET --chain-id=$0G_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
0gchaind tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$0G_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
0gchaind tx distribution withdraw-rewards $0G_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$0G_CHAIN_ID
```

### Validator management
Edit validator
```
0gchaind tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$0G_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
0gchaind tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$0G_CHAIN_ID \
  --gas=auto
```
