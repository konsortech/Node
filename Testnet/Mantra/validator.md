Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
mantrachaind keys add $WALLET
```

To recover your wallet using seed phrase
```
mantrachaind keys add $WALLET --recover
```

Show your wallet list
```
mantrachaind keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
MANTRA_WALLET_ADDRESS=$(mantrachaind keys show $WALLET -a)
MANTRA_VALOPER_ADDRESS=$(mantrachaind keys show $WALLET --bech val -a)
echo 'export MANTRA_WALLET_ADDRESS='${MANTRA_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export MANTRA_VALOPER_ADDRESS='${MANTRA_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
Faucet available on website
```
https://faucet.testnet.mantrachain.io/
```

### Create validator

check your wallet balance:
```
mantrachaind query bank balances $MANTRA_WALLET_ADDRESS
```
To create your validator run command below
```
mantrachaind tx staking create-validator \
  --amount 10000000uaum \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey $(mantrachaind tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $MANTRA_CHAIN_ID
  --gas-adjustment 1.4 \
  --gas=auto 
```

### Check your validator key
```
[[ $(mantrachaind q staking validator $MANTRA_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(mantrachaind status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
mantrachaind q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu mantrachaind -o cat
```

Start service
```
sudo systemctl start mantrachaind
```

Stop service
```
sudo systemctl stop mantrachaind
```

Restart service
```
sudo systemctl restart mantrachaind
```

### Node info
Synchronization info
```
mantrachaind status 2>&1 | jq .SyncInfo
```

Validator info
```
mantrachaind status 2>&1 | jq .ValidatorInfo
```

Node info
```
mantrachaind status 2>&1 | jq .NodeInfo
```

Show node id
```
mantrachaind tendermint show-node-id
```

### Wallet operations
List of wallets
```
mantrachaind keys list
```

Recover wallet
```
mantrachaind keys add $WALLET --recover
```

Delete wallet
```
mantrachaind keys delete $WALLET
```

Get wallet balance
```
mantrachaind query bank balances $MANTRA_WALLET_ADDRESS
```

Transfer funds
```
mantrachaind tx bank send $MANTRA_WALLET_ADDRESS <TO_MANTRA_WALLET_ADDRESS> 10000000uaum
```

### Voting
```
mantrachaind tx gov vote 1 yes --from $WALLET --chain-id=$MANTRA_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
mantrachaind tx staking delegate $MANTRA_VALOPER_ADDRESS 10000000uaum --from=$WALLET --chain-id=$MANTRA_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
mantrachaind tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000uaum --from=$WALLET --chain-id=$MANTRA_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
mantrachaind tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$MANTRA_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
mantrachaind tx distribution withdraw-rewards $MANTRA_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$MANTRA_CHAIN_ID
```

### Validator management
Edit validator
```
mantrachaind tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$MANTRA_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
mantrachaind tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$MANTRA_CHAIN_ID \
  --gas=auto
```
