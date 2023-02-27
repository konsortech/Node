## State Sync
```
sudo systemctl stop curiumd

cp $HOME/.curium/data/priv_validator_state.json $HOME/.curium/priv_validator_state.json.backup
curiumd tendermint unsafe-reset-all --home $HOME/.curium

SNAP_RPC="https://mainnet-bluzelle-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="485ddd819bb13d4c1eb21e0aa46810e3c1f9d7b1@mainnet-bluzelle.konsortech.xyz:26656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.curium/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.curium/config/config.toml

mv $HOME/.curium/priv_validator_state.json.backup $HOME/.curium/data/priv_validator_state.json

sudo systemctl restart curiumd
sudo journalctl -u curiumd -f --no-hostname -o cat
```
