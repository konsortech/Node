## State Sync
```
sudo systemctl stop misestmd

cp $HOME/.misestm/data/priv_validator_state.json $HOME/.misestm/priv_validator_state.json.backup
misestmd unsafe-reset-all
curl https://raw.githubusercontent.com/konsortech/Node/main/Mainnet/Mises/addrbook.json > $HOME/.misestm/config/addrbook.json

SNAP_RPC="https://mainnet-mises-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="aa33a396fcde81b9826df913e40600e4c2cade1f@mainnet-mises.konsortech.xyz:15656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.misestm/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.misestm/config/config.toml

mv $HOME/.misestm/priv_validator_state.json.backup $HOME/.misestm/data/priv_validator_state.json

sudo systemctl restart misestmd
sudo journalctl -u misestmd -f --no-hostname -o cat
```
