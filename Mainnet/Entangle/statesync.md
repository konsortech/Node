## Statesync
```
sudo service entangled stop
cp $HOME/.entangled/data/priv_validator_state.json $HOME/.entangled/priv_validator_state.json.backup
entangled tendermint unsafe-reset-all --home $HOME/.entangled --keep-addr-book

SYNC_RPC="https://mainnet-entangle-rpc.konsortech.xyz:443"
SYNC_PEER="7378259114db42b8b46fad4cc9f31d32a5419b60@mainnet-entangle.konsortech.xyz:12656"
LATEST_HEIGHT=$(curl -s $SYNC_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SYNC_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

sed -i \
  -e "s|^enable *=.*|enable = true|" \
  -e "s|^rpc_servers *=.*|rpc_servers = \"$SYNC_RPC,$SYNC_RPC\"|" \
  -e "s|^trust_height *=.*|trust_height = $BLOCK_HEIGHT|" \
  -e "s|^trust_hash *=.*|trust_hash = \"$TRUST_HASH\"|" \
  -e "s|^persistent_peers *=.*|persistent_peers = \"$SYNC_PEER\"|" \
  $HOME/.entangled/config/config.toml

mv $HOME/.entangled/priv_validator_state.json.backup $HOME/.entangled/data/priv_validator_state.json

sudo service entangled restart && sudo journalctl -fu entangled -o cat
```
