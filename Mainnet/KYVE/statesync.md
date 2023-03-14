## State Sync
```
sudo systemctl stop kyved

cp $HOME/.kyve/data/priv_validator_state.json $HOME/.kyve/priv_validator_state.json.backup
kyved tendermint unsafe-reset-all --home $HOME/.kyve --keep-addr-book

SNAP_RPC="https://mainnet-kyve-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="b6a030993f185fe2c224ecdebc379de07fe4d2c1@mainnet-kyved.konsortech.xyz:18656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.kyve/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.kyve/config/config.toml

mv $HOME/.kyve/priv_validator_state.json.backup $HOME/.kyve/data/priv_validator_state.json

sudo systemctl restart kyved
sudo journalctl -u kyved -f --no-hostname -o cat
```
