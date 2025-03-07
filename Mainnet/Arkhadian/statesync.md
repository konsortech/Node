## State Sync
```
sudo systemctl stop arkh

cp $HOME/.arkh/data/priv_validator_state.json $HOME/.arkh/priv_validator_state.json.backup
arkh tendermint unsafe-reset-all --home $HOME/.arkh --keep-addr-book

SNAP_RPC="https://mainnet-arkh-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="b4f3bd0b9202be699635966978b44e5ea8ab9fba@mainnet-arkh.konsortech.xyz:26656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.arkh/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.arkh/config/config.toml

mv $HOME/.arkh/priv_validator_state.json.backup $HOME/.arkh/data/priv_validator_state.json

sudo systemctl restart arkh
sudo journalctl -u arkh -f --no-hostname -o cat
```
