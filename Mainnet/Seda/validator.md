Guide for Validator CLI

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
sedad keys add $WALLET
```

To recover your wallet using seed phrase
```
sedad keys add $WALLET --recover
```

Show your wallet list
```
sedad keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
SEDA_WALLET_ADDRESS=$(sedad keys show $WALLET -a)
SEDA_VALOPER_ADDRESS=$(sedad keys show $WALLET --bech val -a)
echo 'export SEDA_WALLET_ADDRESS='${SEDA_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export SEDA_VALOPER_ADDRESS='${SEDA_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
```
buy some $SEDA on osmosis zone
```

### Create validator

check your wallet balance:
```
sedad query bank balances $SEDA_WALLET_ADDRESS
```
To create your validator run command below
```
sedad comet show-validator

```
The output will be similar to this (with a different key):

```
{"@type":"/cosmos.crypto.ed25519.PubKey","key":"lR1d7YBVK5jYijOfWVKRFoWCsS4dg3kagT7LB9GnG8I="}
```
Then, create a file named validator.json with the following content:

```
{    
    "pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"lR1d7YBVK5jYijOfWVKRFoWCsS4dg3kagT7LB9GnG8I="},
    "amount": "1000000000000000000aseda",
    "moniker": "your-node-moniker",
    "identity": "keybase mainnet validator",
    "website": "optional website for your validator",
    "security": "optional security contact for your validator",
    "details": "optional details for your validator",
    "commission-rate": "0.1",
    "commission-max-rate": "0.2",
    "commission-max-change-rate": "0.01",
    "min-self-delegation": "1"
}
```

### Check your validator key
```
[[ $(sedad q staking validator $SEDA_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(sedad status | jq -r .validator_info.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
sedad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu sedad -o cat
```

Start service
```
sudo systemctl start sedad
```

Stop service
```
sudo systemctl stop sedad
```

Restart service
```
sudo systemctl restart sedad
```

### Node info
Synchronization info
```
sedad status 2>&1 | jq .sync_info
```

Validator info
```
sedad status 2>&1 | jq .validator_info
```

Node info
```
sedad status 2>&1 | jq .node_info
```

Show node id
```
sedad tendermint show-node-id
```

### Wallet operations
List of wallets
```
sedad keys list
```

Recover wallet
```
sedad keys add $WALLET --recover
```

Delete wallet
```
sedad keys delete $WALLET
```

Get wallet balance
```
sedad query bank balances $SEDA_WALLET_ADDRESS
```

Transfer funds
```
sedad tx bank send $SEDA_WALLET_ADDRESS <TO_SEDA_WALLET_ADDRESS> 10000000000aseda
```

### Voting
```
sedad tx gov vote 1 yes --from $WALLET --chain-id=$SEDA_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
sedad tx staking delegate $SEDA_VALOPER_ADDRESS 10000000000aseda --from=$WALLET --chain-id=$SEDA_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
sedad tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000000aseda --from=$WALLET --chain-id=$SEDA_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
sedad tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$SEDA_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
sedad tx distribution withdraw-rewards $SEDA_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$SEDA_CHAIN_ID
```

### Validator management
Edit validator
```
sedad tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$SEDA_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
sedad tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$SEDA_CHAIN_ID \
  --gas=auto
```


1000000000000000000aseda
