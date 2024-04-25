## State Sync
```
sudo systemctl stop sided

cp $HOME/.side/data/priv_validator_state.json $HOME/.side/priv_validator_state.json.backup
sided tendermint unsafe-reset-all --home $HOME/.side --keep-addr-book

SNAP_RPC="https://testnet-side-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="b6b0a74dbc8fe2c6a96b00ef420e11b44e36f945@testnet-side.konsortech.xyz:22656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.side/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.side/config/config.toml

mv $HOME/.side/priv_validator_state.json.backup $HOME/.side/data/priv_validator_state.json

rm -rf ~/.side/wasm
curl -L https://snap1.konsortech.xyz/side-testnet/wasm.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.side

sudo systemctl restart sided
sudo journalctl -u sided -f --no-hostname -o cat
```
