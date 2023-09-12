Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
selfchaind keys add $WALLET
```

To recover your wallet using seed phrase
```
selfchaind keys add $WALLET --recover
```

Show your wallet list
```
selfchaind keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
SELF_WALLET_ADDRESS=$(selfchaind keys show $WALLET -a)
SELF_VALOPER_ADDRESS=$(selfchaind keys show $WALLET --bech val -a)
echo 'export SELF_WALLET_ADDRESS='${SELF_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export SELF_VALOPER_ADDRESS='${SELF_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
God to discord channel and request #testnet-faucet
```
N/A
```

### Create validator

check your wallet balance:
```
selfchaind query bank balances $SELF_WALLET_ADDRESS
```
To create your validator run command below
```
selfchaind tx staking create-validator \
  --amount 1000000000uself \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(selfchaind tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $SELF_CHAIN_ID
  --gas-prices==0.1uself \
  --gas-adjustment=1.5 \
  --gas=auto \
```

### Check your validator key
```
[[ $(selfchaind q staking validator $SELF_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(selfchaind status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
selfchaind q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu selfchaind -o cat
```

Start service
```
sudo systemctl start selfchaind
```

Stop service
```
sudo systemctl stop selfchaind
```

Restart service
```
sudo systemctl restart selfchaind
```

### Node info
Synchronization info
```
selfchaind status 2>&1 | jq .SyncInfo
```

Validator info
```
selfchaind status 2>&1 | jq .ValidatorInfo
```

Node info
```
selfchaind status 2>&1 | jq .NodeInfo
```

Show node id
```
selfchaind tendermint show-node-id
```

### Wallet operations
List of wallets
```
selfchaind keys list
```

Recover wallet
```
selfchaind keys add $WALLET --recover
```

Delete wallet
```
selfchaind keys delete $WALLET
```

Get wallet balance
```
selfchaind query bank balances $SELF_WALLET_ADDRESS
```

Transfer funds
```
selfchaind tx bank send $SELF_WALLET_ADDRESS <TO_SELF_WALLET_ADDRESS> 1000000000uself
```

### Voting
```
selfchaind tx gov vote 1 yes --from $WALLET --chain-id=$SELF_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
selfchaind tx staking delegate $SELF_VALOPER_ADDRESS 1000000000uself --from=$WALLET --chain-id=$SELF_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
selfchaind tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000000uself --from=$WALLET --chain-id=$SELF_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
selfchaind tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$SELF_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
selfchaind tx distribution withdraw-rewards $SELF_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$SELF_CHAIN_ID
```

### Validator management
Edit validator
```
selfchaind tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$SELF_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
selfchaind tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$SELF_CHAIN_ID \
  --gas=auto
```
