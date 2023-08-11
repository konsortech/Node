## State Sync
```

sudo systemctl stop blockxd

cp $HOME/.blockxd/data/priv_validator_state.json $HOME/.blockxd/priv_validator_state.json.backup
blockxd tendermint unsafe-reset-all --home $HOME/.blockxd --keep-addr-book

SNAP_RPC="https://testnet-blockx-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="f0f63a19eb749e368cb02a541b7ef53689d8a1f9@testnet-blockx.konsortech.xyz:18656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.blockxd/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.blockxd/config/config.toml

mv $HOME/.blockxd/priv_validator_state.json.backup $HOME/.blockxd/data/priv_validator_state.json

sudo systemctl restart blockxd
sudo journalctl -u blockxd -f --no-hostname -o cat
