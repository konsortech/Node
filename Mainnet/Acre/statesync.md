## State Sync
```
sudo systemctl stop acred

cp $HOME/.acred/data/priv_validator_state.json $HOME/.acred/priv_validator_state.json.backup
acred tendermint unsafe-reset-all --home $HOME/.acred --keep-addr-book

SNAP_RPC="https://mainnet-acre-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="7d630b6e517598b4dc84a07c15fe328709a2705b@mainnet-acre.konsortech.xyz:26656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.acred/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.acred/config/config.toml

mv $HOME/.acred/priv_validator_state.json.backup $HOME/.acred/data/priv_validator_state.json

sudo systemctl restart acred
sudo journalctl -u acred -f --no-hostname -o cat
```
