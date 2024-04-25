## Guidance for Validator

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
sided keys add $WALLET
```

To recover your wallet using seed phrase
```
sided keys add $WALLET --recover
```

Show your wallet list
```
sided keys list
```

### Save wallet info
Add wallet and validator address into variables 
```
SIDE_WALLET_ADDRESS=$(sided keys show $WALLET -a)
SIDE_VALOPER_ADDRESS=$(sided keys show $WALLET --bech val -a)
echo 'export SIDE_WALLET_ADDRESS='${SIDE_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export SIDE_VALOPER_ADDRESS='${SIDE_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Fund your wallet
In order to create validator first you need to fund your wallet with discord faucet.
```
go to discord channel and #testnet-faucet channel
type 
$request side-testnet-3 bcxxxxxxxxx
```

### Create validator

check your wallet balance:
```
sided query bank balances $SIDE_WALLET_ADDRESS
```

To create your validator run command below
```
sided tx staking create-validator \
--amount 1000000uside \
--pubkey $(sided tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id side-testnet-3 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.05 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.005uside \
-y
```

### Check your validator key
```
[[ $(sided q staking validator $SIDE_VALOPER_ADDRESS -oj | jq -r .consensus_pubkey.key) = $(sided status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"
```

### Get list of validators
```
sided q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

## Usefull commands
### Service management
Check logs
```
journalctl -fu sided -o cat
```

Start service
```
sudo systemctl start sided
```

Stop service
```
sudo systemctl stop sided
```

Restart service
```
sudo systemctl restart sided
```

### Node info
Synchronization info
```
sided status 2>&1 | jq .SyncInfo
```

Validator info
```
sided status 2>&1 | jq .ValidatorInfo
```

Node info
```
sided status 2>&1 | jq .NodeInfo
```

Show node id
```
sided tendermint show-node-id
```

### Wallet operations
List of wallets
```
sided keys list
```

Recover wallet
```
sided keys add $WALLET --recover
```

Delete wallet
```
sided keys delete $WALLET
```

Get wallet balance
```
sided query bank balances $SIDE_WALLET_ADDRESS
```

Transfer funds
```
sided tx bank send $SIDE_WALLET_ADDRESS <TO_SIDE_WALLET_ADDRESS> 1000000uside
```

### Voting
```
sided tx gov vote 1 yes --from $WALLET --chain-id=$SIDE_CHAIN_ID
```

### Staking, Delegation and Rewards
Delegate stake
```
sided tx staking delegate $SIDE_VALOPER_ADDRESS 1000000uside --from=$WALLET --chain-id=$SIDE_CHAIN_ID --gas=auto
```

Redelegate stake from validator to another validator
```
sided tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 1000000uside --from=$WALLET --chain-id=$SIDE_CHAIN_ID --gas=auto
```

Withdraw all rewards
```
sided tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$SIDE_CHAIN_ID --gas=auto
```

Withdraw rewards with commision
```
sided tx distribution withdraw-rewards $SIDE_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$SIDE_CHAIN_ID
```

### Validator management
Edit validator
```
sided tx staking edit-validator \
  --moniker=$NODENAME \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=$SIDE_CHAIN_ID \
  --from=$WALLET
```

Unjail validator
```
sided tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$SIDE_CHAIN_ID \
  --gas=auto
```
