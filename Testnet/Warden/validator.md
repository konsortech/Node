## Guidance for Validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
wardend keys add $WALLET
```

To recover your wallet using seed phrase
```
wardend keys add $WALLET --recover
```

Show your wallet list
```
wardend keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
WARDEN_WALLET_ADDRESS=$(wardend keys show $WALLET -a)
WARDEN_VALOPER_ADDRESS=$(wardend keys show $WALLET --bech val -a)
echo 'export WARDEN_WALLET_ADDRESS='${WARDEN_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export WARDEN_VALOPER_ADDRESS='${WARDEN_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with discord faucet.
```
curl -X POST -H "Content-Type: application/json" -d '{"address": "wardenxxxxx"}' https://faucet.alfama.wardenprotocol.org
```

### Create validator

check your wallet balance:
```
wardend query bank balances $WARDEN_WALLET_ADDRESS
```
To create your validator run command below
```
wardend comet show-validator

```
The output will be similar to this (with a different key):

```
{"@type":"/cosmos.crypto.ed25519.PubKey","key":"lR1d7YBVK5jYijOfWVKRFoWCsS4dg3kagT7LB9GnG8I="}
```
Then, create a file named validator.json with the following content:

```
{    
    "pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"lR1d7YBVK5jYijOfWVKRFoWCsS4dg3kagT7LB9GnG8I="},
    "amount": "1000000uward",
    "moniker": "your-node-moniker",
    "identity": "eqlab testnet validator",
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
wardend tx staking create-validator validator.json \
    --from=$WALLET \
    --chain-id=alfama \
    --fees=500uward
```

### Check your validator key
```
[[ $(wardend q staking validator $WARDEN_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(wardend status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
wardend q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu wardend -o cat
```

Start service
```
sudo systemctl start wardend
```

Stop service
```
sudo systemctl stop wardend
```

Restart service
```
sudo systemctl restart wardend
```

### Node info
Synchronization info
```
wardend status 2>&1 | jq .SyncInfo
```

Validator info
```
wardend status 2>&1 | jq .ValidatorInfo
```

Node info
```
wardend status 2>&1 | jq .NodeInfo
```

Show node id
```
wardend tendermint show-node-id
```

### Wallet operations
List of wallets
```
wardend keys list
```

Recover wallet
```
wardend keys add $WALLET --recover
```

Delete wallet
```
wardend keys delete $WALLET
```

Get wallet balance
```
wardend query bank balances $WARDEN_WALLET_ADDRESS
```

Transfer funds
```
wardend tx bank send $WARDEN_WALLET_ADDRESS <TO_WARDEN_WALLET_ADDRESS> 1000000uward
```

### Voting
```
wardend tx gov vote 1 yes --from $WALLET --chain-id=$WARDEN_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
wardend tx staking delegate $WARDEN_VALOPER_ADDRESS 1000000uward --from=$WALLET --chain-id=$WARDEN_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
wardend tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000uward --from=$WALLET --chain-id=$WARDEN_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
wardend tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$WARDEN_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
wardend tx distribution withdraw-rewards $WARDEN_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$WARDEN_CHAIN_ID
```

### Validator management
Edit validator
```
wardend tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$WARDEN_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
wardend tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$WARDEN_CHAIN_ID \
  --gas=auto
```
