## Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
paxid keys add $WALLET
```

To recover your wallet using seed phrase
```
paxid keys add $WALLET --recover
```

Show your wallet list
```
paxid keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
PAXI_WALLET_ADDRESS=$(paxid keys show $WALLET -a)
PAXI_VALOPER_ADDRESS=$(paxid keys show $WALLET --bech val -a)
echo 'export PAXI_WALLET_ADDRESS='${PAXI_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export PAXI_VALOPER_ADDRESS='${PAXI_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet

```
Buy on Osmosis DEX
```

### Create validator

check your wallet balance:
```
paxid query bank balances $PAXI_WALLET_ADDRESS
```
To create your validator run command below
```
paxid tendermint show-validator
```
```
nano ~/go/bin/paxi/validator.json
```
filled your validator info
```
{
  "pubkey":  result_from_above,
  "amount": "1000000upaxi",
  "moniker": "your-moniker",
  "identity": "your-identity",
  "website": "your-website",
  "security": "your-mail",
  "details": "your-detail",
  "commission-rate": "0.05",
  "commission-max-rate": "0.2",
  "commission-max-change-rate": "0.05",
  "min-self-delegation": "1"
}
```
```
paxid tx staking create-validator ~/go/bin/paxi/validator.json \
--from wallet \
--chain-id paxi-mainnet \
--fees 10000upaxi
```

### Check your validator key
```
[[ $(paxid q staking validator $PAXI_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(paxid status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
paxid q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu paxid -o cat
```

Start service
```
sudo systemctl start paxid
```

Stop service
```
sudo systemctl stop paxid
```

Restart service
```
sudo systemctl restart paxid
```

### Node info
Synchronization info
```
paxid status 2>&1 | jq .SyncInfo
```

Validator info
```
paxid status 2>&1 | jq .ValidatorInfo
```

Node info
```
paxid status 2>&1 | jq .NodeInfo
```

Show node id
```
paxid tendermint show-node-id
```

### Wallet operations
List of wallets
```
paxid keys list
```

Recover wallet
```
paxid keys add $WALLET --recover
```

Delete wallet
```
paxid keys delete $WALLET
```

Get wallet balance
```
paxid query bank balances $PAXI_WALLET_ADDRESS
```

Transfer funds
```
paxid tx bank send $PAXI_WALLET_ADDRESS <TO_PAXI_WALLET_ADDRESS> 1000000upaxi
```

### Voting
```
paxid tx gov vote 1 yes --from $WALLET --chain-id=$PAXI_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
paxid tx staking delegate $PAXI_VALOPER_ADDRESS 1000000upaxi --from=$WALLET --chain-id=$PAXI_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
paxid tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000upaxi --from=$WALLET --chain-id=$PAXI_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
paxid tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$PAXI_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
paxid tx distribution withdraw-rewards $PAXI_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$PAXI_CHAIN_ID
```

### Validator management
Edit validator
```
paxid tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$PAXI_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
paxid tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$PAXI_CHAIN_ID \
  --gas=auto
```
