## Statesync

```
sudo systemctl stop iris

cp $HOME/.iris/data/priv_validator_state.json $HOME/.iris/priv_validator_state.json.backup
iris tendermint unsafe-reset-all --home $HOME/.iris --keep-addr-book

SNAP_RPC="https://mainnet-iris-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.iris/config/config.toml

mv $HOME/.iris/priv_validator_state.json.backup $HOME/.iris/data/priv_validator_state.json

sudo systemctl restart iris
sudo journalctl -u iris -f --no-hostname -o cat
```
