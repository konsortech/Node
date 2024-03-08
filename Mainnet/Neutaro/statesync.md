## State Sync
```
sudo systemctl stop Neutaro

cp $HOME/.Neutaro/data/priv_validator_state.json $HOME/.Neutaro/priv_validator_state.json.backup
Neutaro tendermint unsafe-reset-all --home $HOME/.Neutaro --keep-addr-book

SNAP_RPC="https://mainnet-neutaro-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="ca241438087e75bffcf5bd6739da0c5fc6cdaf60@mainnet-neutaro.konsortech.xyz:14656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.Neutaro/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.Neutaro/config/config.toml

mv $HOME/.Neutaro/priv_validator_state.json.backup $HOME/.Neutaro/data/priv_validator_state.json

sudo systemctl restart Neutaro
sudo journalctl -u Neutaro -f --no-hostname -o cat
```
