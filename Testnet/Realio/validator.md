Guidence for create validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
realio-networkd keys add $WALLET
```

To recover your wallet using seed phrase
```
realio-networkd keys add $WALLET --recover
```

Show your wallet list
```
realio-networkd keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
REALIO_WALLET_ADDRESS=$(realio-networkd keys show $WALLET -a)
REALIO_VALOPER_ADDRESS=$(realio-networkd keys show $WALLET --bech val -a)
echo 'export REALIO_WALLET_ADDRESS='${REALIO_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export REALIO_VALOPER_ADDRESS='${REALIO_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with discord faucet.
```
Go to Discord https://discord.gg/jqQ26CYmuR
channel 
```

### Create validator

check your wallet balance:
```
realio-networkd query bank balances $REALIO_WALLET_ADDRESS
```
To create your validator run command below
```
realio-networkd tx staking create-validator \
  --amount 100000ario \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(realio-networkd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $REALIO_CHAIN_ID
```

### Check your validator key
```
[[ $(realio-networkd q staking validator $REALIO_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(realio-networkd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
realio-networkd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu realio-networkd -o cat
```

Start service
```
sudo systemctl start realio-networkd
```

Stop service
```
sudo systemctl stop realio-networkd
```

Restart service
```
sudo systemctl restart realio-networkd
```

### Node info
Synchronization info
```
realio-networkd status 2>&1 | jq .SyncInfo
```

Validator info
```
realio-networkd status 2>&1 | jq .ValidatorInfo
```

Node info
```
realio-networkd status 2>&1 | jq .NodeInfo
```

Show node id
```
realio-networkd tendermint show-node-id
```

### Wallet operations
List of wallets
```
realio-networkd keys list
```

Recover wallet
```
realio-networkd keys add $WALLET --recover
```

Delete wallet
```
realio-networkd keys delete $WALLET
```

Get wallet balance
```
realio-networkd query bank balances $REALIO_WALLET_ADDRESS
```

Transfer funds
```
realio-networkd tx bank send $REALIO_WALLET_ADDRESS <TO_REALIO_WALLET_ADDRESS> 100000ario
```

### Voting
```
realio-networkd tx gov vote 1 yes --from $WALLET --chain-id=$REALIO_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
realio-networkd tx staking delegate $REALIO_VALOPER_ADDRESS 100000ario --from=$WALLET --chain-id=$REALIO_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
realio-networkd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 100000ario --from=$WALLET --chain-id=$REALIO_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
realio-networkd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$REALIO_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
realio-networkd tx distribution withdraw-rewards $REALIO_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$REALIO_CHAIN_ID
```

### Validator management
Edit validator
```
realio-networkd tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$REALIO_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
realio-networkd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$REALIO_CHAIN_ID \
  --gas=auto
```
