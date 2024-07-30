## State Sync
```

sudo systemctl stop symphonyd

cp $HOME/.symphonyd/data/priv_validator_state.json $HOME/.symphonyd/priv_validator_state.json.backup
symphonyd tendermint unsafe-reset-all --home $HOME/.symphonyd --keep-addr-book

SNAP_RPC="https://mainnet-symphony-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="8e5d8b822c67b9e60f884857c2b58af95e1c67be@testnet-symphony.konsortech.xyz:14656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.symphonyd/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.symphonyd/config/config.toml

mv $HOME/.symphonyd/priv_validator_state.json.backup $HOME/.symphonyd/data/priv_validator_state.json

sudo systemctl restart symphonyd
sudo journalctl -u symphonyd -f --no-hostname -o cat
```
