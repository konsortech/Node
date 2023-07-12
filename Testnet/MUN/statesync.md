## State Sync
```
sudo systemctl stop mund

cp $HOME/.mun/data/priv_validator_state.json $HOME/.mun/priv_validator_state.json.backup
mund tendermint unsafe-reset-all --home $HOME/.mun --keep-addr-book

SNAP_RPC="https://testnet-mun-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.mun/config/config.toml

mv $HOME/.mun/priv_validator_state.json.backup $HOME/.mun/data/priv_validator_state.json

sudo systemctl restart mund
sudo journalctl -u mund -f --no-hostname -o cat
