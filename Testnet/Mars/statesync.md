## State Sync
```
sudo systemctl stop marsd

cp $HOME/.mars/data/priv_validator_state.json $HOME/.mars/priv_validator_state.json.backup
marsd tendermint unsafe-reset-all --home $HOME/.mars --keep-addr-book

SNAP_RPC="https://testnet-mars-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="2af38e7f0b92e26f38cacdf68d1817f9492a34a2@testnet-mars.konsortech.xyz:26656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.mars/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.mars/config/config.toml

mv $HOME/.mars/priv_validator_state.json.backup $HOME/.mars/data/priv_validator_state.json

sudo systemctl restart marsd
sudo journalctl -u marsd -f --no-hostname -o cat
```
