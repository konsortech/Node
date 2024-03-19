## Guidance for Validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
uniond keys add $WALLET
```

To recover your wallet using seed phrase
```
uniond keys add $WALLET --recover
```

Show your wallet list
```
uniond keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
UNION_WALLET_ADDRESS=$(uniond keys show $WALLET -a)
UNION_VALOPER_ADDRESS=$(uniond keys show $WALLET --bech val -a)
echo 'export UNION_WALLET_ADDRESS='${UNION_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export UNION_VALOPER_ADDRESS='${UNION_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with discord faucet.
```
N/A
```

### Create validator

check your wallet balance:
```
uniond query bank balances $UNION_WALLET_ADDRESS
```
To create your validator run command below
```
uniond comet show-validator

```
The output will be similar to this (with a different key):

```
{"@type":"/cosmos.crypto.bn254.PubKey","key":"lR1d7YBVK5jYijOfWVKRFoWCsS4dg3kagT7LB9GnG8I="}
```
Then, create a file named validator.json with the following content:

```
{    
    "pubkey": {"@type":"/cosmos.crypto.bn254.PubKey","key":"lR1d7YBVK5jYijOfWVKRFoWCsS4dg3kagT7LB9GnG8I="},
    "amount": "1000000muno",
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
uniond tx staking create-validator validator.json \
    --from=$WALLET \
    --chain-id=$UNION_CHAIN_ID \
    --gas-adjustment 1.4 \
    --gas auto \
    --gas-prices 0muno
```

### Check your validator key
```
[[ $(uniond q staking validator $UNION_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(uniond status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
uniond q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu uniond -o cat
```

Start service
```
sudo systemctl start uniond
```

Stop service
```
sudo systemctl stop uniond
```

Restart service
```
sudo systemctl restart uniond
```

### Node info
Synchronization info
```
uniond status 2>&1 | jq .SyncInfo
```

Validator info
```
uniond status 2>&1 | jq .ValidatorInfo
```

Node info
```
uniond status 2>&1 | jq .NodeInfo
```

Show node id
```
uniond tendermint show-node-id
```

### Wallet operations
List of wallets
```
uniond keys list
```

Recover wallet
```
uniond keys add $WALLET --recover
```

Delete wallet
```
uniond keys delete $WALLET
```

Get wallet balance
```
uniond query bank balances $UNION_WALLET_ADDRESS
```

Transfer funds
```
uniond tx bank send $UNION_WALLET_ADDRESS <TO_UNION_WALLET_ADDRESS> 1000000muno
```

### Voting
```
uniond tx gov vote 1 yes --from $WALLET --chain-id=$UNION_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
uniond tx staking delegate $UNION_VALOPER_ADDRESS 1000000muno --from=$WALLET --chain-id=$UNION_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
uniond tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000muno --from=$WALLET --chain-id=$UNION_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
uniond tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$UNION_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
uniond tx distribution withdraw-rewards $UNION_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$UNION_CHAIN_ID
```

### Validator management
Edit validator
```
uniond tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$UNION_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
uniond tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$UNION_CHAIN_ID \
  --gas=auto
```
