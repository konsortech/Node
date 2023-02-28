## State Sync
```
sudo systemctl stop lumenxd

cp $HOME/.lumenx/data/priv_validator_state.json $HOME/.lumenx/priv_validator_state.json.backup
lumenxd tendermint unsafe-reset-all --home $HOME/.lumenx --keep-addr-book

SNAP_RPC="https://mainnet-acre-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="dc32e90bf2321b220bc2346fa01425117372107a@mainnet-lumenx.konsortech.xyz:22656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.lumenx/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.lumenx/config/config.toml

mv $HOME/.lumenx/priv_validator_state.json.backup $HOME/.lumenx/data/priv_validator_state.json

sudo systemctl restart lumenxd
sudo journalctl -u lumenxd -f --no-hostname -o cat
```
