## State Sync
```
sudo systemctl stop namadad

sudo cp $HOME/.local/share/namada/namada.5f5de2dd1b88cba30586420/cometbft/data/priv_validator_state.json $HOME/.local/share/namada/namada.5f5de2dd1b88cba30586420/cometbft/priv_validator_state.json.backup

rm -rf $HOME/.local/share/namada/namada.5f5de2dd1b88cba30586420/db
rm -rf $HOME/.local/share/namada/namada.5f5de2dd1b88cba30586420/cometbft/data

SNAP_RPC="https://mainnet-namada-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
echo "Latest: $LATEST_HEIGHT | Trust Height: $BLOCK_HEIGHT | Hash: $TRUST_HASH"

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.local/share/namada/namada.5f5de2dd1b88cba30586420/config.toml

sudo cp $HOME/.local/share/namada/namada.5f5de2dd1b88cba30586420/cometbft/priv_validator_state.json.backup $HOME/.local/share/namada/namada.5f5de2dd1b88cba30586420/cometbft/data/priv_validator_state.json

sudo systemctl start namadad && journalctl -fu namadad -o cat
```
