## Guide CLI Command

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
entangled keys add $WALLET
```

To recover your wallet using seed phrase
```
entangled keys add $WALLET --recover
```

Show your wallet list
```
entangled keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
ENTANGLE_WALLET_ADDRESS=$(entangled keys show $WALLET -a)
ENTANGLE_VALOPER_ADDRESS=$(entangled keys show $WALLET --bech val -a)
echo 'export ENTANGLE_WALLET_ADDRESS='${ENTANGLE_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export ENTANGLE_VALOPER_ADDRESS='${ENTANGLE_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
export your wallet to evm private key and import into that private key into metamask
```
entangled keys unsafe-export-eth-key $WALLET
```

### Create validator

check your wallet balance:
```
entangled query bank balances $ENTANGLE_WALLET_ADDRESS
```
To create your validator run command below
```
entangled tx staking create-validator \
  --amount 5000000000000000000aNGL \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(entangled tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $ENTANGLE_CHAIN_ID
  --gas=500000 \
  --gas-prices="10aNGL" 
```

### Check your validator key
```
[[ $(entangled q staking validator $ENTANGLE_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(entangled status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
entangled q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu entangled -o cat
```

Start service
```
sudo systemctl start entangled
```

Stop service
```
sudo systemctl stop entangled
```

Restart service
```
sudo systemctl restart entangled
```

### Node info
Synchronization info
```
entangled status 2>&1 | jq .SyncInfo
```

Validator info
```
entangled status 2>&1 | jq .ValidatorInfo
```

Node info
```
entangled status 2>&1 | jq .NodeInfo
```

Show node id
```
entangled tendermint show-node-id
```

### Wallet operations
List of wallets
```
entangled keys list
```

Recover wallet
```
entangled keys add $WALLET --recover
```

Delete wallet
```
entangled keys delete $WALLET
```

Get wallet balance
```
entangled query bank balances $ENTANGLE_WALLET_ADDRESS
```

Transfer funds
```
entangled tx bank send $ENTANGLE_WALLET_ADDRESS <TO_ENTANGLE_WALLET_ADDRESS> 5000000000000000000aNGL
```

### Voting
```
entangled tx gov vote 1 yes --from $WALLET --chain-id=$ENTANGLE_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
entangled tx staking delegate $ENTANGLE_VALOPER_ADDRESS 5000000000000000000aNGL --from=$WALLET --chain-id=$ENTANGLE_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
entangled tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 5000000000000000000aNGL --from=$WALLET --chain-id=$ENTANGLE_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
entangled tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$ENTANGLE_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
entangled tx distribution withdraw-rewards $ENTANGLE_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$ENTANGLE_CHAIN_ID
```

### Validator management
Edit validator
```
entangled tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$ENTANGLE_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
entangled tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$ENTANGLE_CHAIN_ID \
  --gas=auto
```
