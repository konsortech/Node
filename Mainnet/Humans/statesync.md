## State Sync
```
sudo systemctl stop humansd

cp $HOME/.humansd/data/priv_validator_state.json $HOME/.humansd/priv_validator_state.json.backup
humansd tendermint unsafe-reset-all --home $HOME/.humansd --keep-addr-book

SNAP_RPC="https://mainnet-humans-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="2f8a0bf63e23606dc85bdd11afbf34e68a9f3b74@mainnet-humans.konsortech.xyz:40656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.humansd/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.humansd/config/config.toml

mv $HOME/.humansd/priv_validator_state.json.backup $HOME/.humansd/data/priv_validator_state.json

sudo systemctl restart humansd
sudo journalctl -u humansd -f --no-hostname -o cat
```
