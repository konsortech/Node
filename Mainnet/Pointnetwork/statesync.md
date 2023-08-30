## State Sync
```
sudo systemctl stop pointd

cp $HOME/.pointd/data/priv_validator_state.json $HOME/.pointd/priv_validator_state.json.backup
pointd tendermint unsafe-reset-all --home $HOME/.pointd --keep-addr-book

SNAP_RPC="https://mainnet-point-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="c827453c21b31da2cc4fc0038f2d331ca14f985e@mainnet-point.konsortech.xyz:29656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.pointd/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.pointd/config/config.toml

mv $HOME/.pointd/priv_validator_state.json.backup $HOME/.pointd/data/priv_validator_state.json

sudo systemctl restart pointd
sudo journalctl -u pointd -f --no-hostname -o cat

```
