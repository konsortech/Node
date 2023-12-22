## State Sync
```
sudo systemctl stop humansd

cp $HOME/.humansd/data/priv_validator_state.json $HOME/.humansd/priv_validator_state.json.backup
humansd tendermint unsafe-reset-all --home $HOME/.humans --keep-addr-book

SNAP_RPC="https://testnet-humans-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.humansd/config/config.toml

mv $HOME/.humansd/priv_validator_state.json.backup $HOME/.humansd/data/priv_validator_state.json

sudo systemctl restart humansd
sudo journalctl -u humansd -f --no-hostname -o cat
```
