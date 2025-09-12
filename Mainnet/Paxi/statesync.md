## State Sync
```
sudo systemctl stop paxid

cp ~/go/bin/paxi/data/priv_validator_state.json ~/go/bin/paxi/priv_validator_state.json.backup
paxid tendermint unsafe-reset-all --home ~/go/bin/paxi --keep-addr-book

SNAP_RPC="https://mainnet-paxi-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="fcf82e0a484c14d0094b7ed6cb8b23fec2bbc3d4@mainnet-paxi.konsortech.xyz:29656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' ~/go/bin/paxi/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" ~/go/bin/paxi/config/config.toml

mv ~/go/bin/paxi/priv_validator_state.json.backup ~/go/bin/paxi/data/priv_validator_state.json

sudo systemctl restart paxid
sudo journalctl -u paxid -f --no-hostname -o cat
```
