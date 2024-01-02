## CLI for Namada

### Wallet Operations

Create a new key:
```
KEY_ALIAS="your_wallet"; namada wallet key gen --alias $KEY_ALIAS
```

Restore executed key:
```
namada wallet key restore --alias $KEY_ALIAS --hd-path default
```

Check your address wallet:
```
namada wallet address find --alias $KEY_ALIAS
```

Fund your wallet
Faucet available on website
```
https://faucet.heliax.click/
```

Check Wallet Balances:
```
namada client balance --owner $KEY_ALIAS
```

Check All Wallet Address:
```
namada wallet key list
```

Send a Transactions:
``` 
namada client transfer --source <SENDER_ADDRESS> --target <RECEIVER_ADDRESS> --token NAM --amount 10 --signing-keys $KEY_ALIAS
```

### Validator Operations
check sync status and node info:
```
curl http://127.0.0.1:26657/status | jq
```

check balance:
```
namada client balance --owner $ALIAS
```

check keys:
```
namada wallet key list
```

stake funds:
```
namadac bond --source $ALIAS --validator $ALIAS --amount 100
```

check your validator bond status:
```
namada client bonds --owner $ALIAS
```

check all bonded nodes:
```
namada client bonded-stake
```

find all the slashes:
```
namada client slashes
```

non-self unbonding:
```
namada client unbond --source aliace --validator $ALIAS --amount 1.2
```

self-unbonding:
```
namada client unbond --validator $ALIAS --amount 0.3
```

withdrawing unbonded tokens:
```
namada client withdraw --source aliace --validator $ALIAS
```

Find Your Validator:
```
namadac find-validator --tm-address=$(curl -s localhost:26657/status | jq -r .result.validator_info.address) --node localhost:26657
```

Check epoch:
```
namada client epoch
```

### Sync and Consensus
check logs:
```
sudo journalctl -u namadad -f
```

check sync status and node info:
```
curl http://127.0.0.1:26657/status | jq
```

check consensus state:
```
curl -s localhost:26657/consensus_state | jq .result.round_state.height_vote_set[0].prevotes_bit_array
```

full consensus state:
```
curl -s localhost:12657/dump_consensus_state
```

your validator votes (prevote):
```
curl -s http://localhost:26657/dump_consensus_state | jq '.result.round_state.votes[0].prevotes' | grep $(curl -s http://localhost:26657/status | jq -r '.result.v
```
