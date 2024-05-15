Guide for Validator CLI

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
junctiond keys add $WALLET
```

To recover your wallet using seed phrase
```
junctiond keys add $WALLET --recover
```

Show your wallet list
```
junctiond keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
AIRCHAINS_WALLET_ADDRESS=$(junctiond keys show $WALLET -a)
AIRCHAINS_VALOPER_ADDRESS=$(junctiond keys show $WALLET --bech val -a)
echo 'export AIRCHAINS_WALLET_ADDRESS='${AIRCHAINS_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export AIRCHAINS_VALOPER_ADDRESS='${AIRCHAINS_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
Faucet available on discord #switchyard-faucet-bot
```
$faucet airxxxxx
```

### Create validator

To create your validator run command below
```
junctiond comet show-validator

```
The output will be similar to this (with a different key) "pubkey":

```
{"@type":"/cosmos.crypto.ed25519.PubKey","key":"xxxxxxxx"}
```
Then, create a file named validator.json with the following content:

```
{    
    "pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"xxxxxxx"},
    "amount": "1000000amf",
    "moniker": "your-node-moniker",
    "identity": "airchain testnet validator",
    "website": "optional website for your validator",
    "security": "optional security contact for your validator",
    "details": "optional details for your validator",
    "commission-rate": "0.1",
    "commission-max-rate": "0.2",
    "commission-max-change-rate": "0.01",
    "min-self-delegation": "1"
}
```
Finally, we're ready to submit the transaction to create the validator:

```
junctiond tx staking create-validator validator.json \
    --from=$WALLET \
    --chain-id=$AIRCHAINS_CHAIN_ID \
    --fees=200amf
```

### Check your validator key
```
[[ $(junctiond q staking validator $AIRCHAINS_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(junctiond status | jq -r .validator_info.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
junctiond q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu junctiond -o cat
```

Start service
```
sudo systemctl start junctiond
```

Stop service
```
sudo systemctl stop junctiond
```

Restart service
```
sudo systemctl restart junctiond
```

### Node info
Synchronization info
```
junctiond status 2>&1 | jq .sync_info
```

Validator info
```
junctiond status 2>&1 | jq .validator_info
```

Node info
```
junctiond status 2>&1 | jq .node_info
```

Show node id
```
junctiond tendermint show-node-id
```

### Wallet operations
List of wallets
```
junctiond keys list
```

Recover wallet
```
junctiond keys add $WALLET --recover
```

Delete wallet
```
junctiond keys delete $WALLET
```

Get wallet balance
```
junctiond query bank balances $AIRCHAINS_WALLET_ADDRESS
```

Transfer funds
```
junctiond tx bank send $AIRCHAINS_WALLET_ADDRESS <TO_AIRCHAINS_WALLET_ADDRESS> 1000000amf
```

### Voting
```
junctiond tx gov vote 1 yes --from $WALLET --chain-id=$AIRCHAINS_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
junctiond tx staking delegate $AIRCHAINS_VALOPER_ADDRESS 1000000amf --from=$WALLET --chain-id=$AIRCHAINS_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
junctiond tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000amf --from=$WALLET --chain-id=$AIRCHAINS_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
junctiond tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$AIRCHAINS_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
junctiond tx distribution withdraw-rewards $AIRCHAINS_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$AIRCHAINS_CHAIN_ID
```

### Validator management
Edit validator
```
junctiond tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$AIRCHAINS_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
junctiond tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$AIRCHAINS_CHAIN_ID \
  --gas=auto
```
