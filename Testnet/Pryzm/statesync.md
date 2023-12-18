## State Sync
```
sudo systemctl stop pryzmd

cp $HOME/.pryzm/data/priv_validator_state.json $HOME/.pryzm/priv_validator_state.json.backup
pryzmd tendermint unsafe-reset-all --home $HOME/.pryzm --keep-addr-book

SNAP_RPC="https://testnet-pryzm-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.pryzm/config/config.toml

mv $HOME/.pryzm/priv_validator_state.json.backup $HOME/.pryzm/data/priv_validator_state.json

sudo systemctl restart pryzmd
sudo journalctl -u pryzmd -f --no-hostname -o cat
```
