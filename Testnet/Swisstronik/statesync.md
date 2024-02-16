## State Sync
```
sudo systemctl stop swisstronikd

cp $HOME/.swisstronik/data/priv_validator_state.json $HOME/.swisstronik/priv_validator_state.json.backup
swisstronikd tendermint unsafe-reset-all --home $HOME/.swisstronik --keep-addr-book

SNAP_RPC="https://testnet-swisstronik-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.swisstronik/config/config.toml

mv $HOME/.swisstronik/priv_validator_state.json.backup $HOME/.swisstronik/data/priv_validator_state.json

sudo systemctl restart swisstronikd
sudo journalctl -u swisstronikd -f --no-hostname -o cat
```
