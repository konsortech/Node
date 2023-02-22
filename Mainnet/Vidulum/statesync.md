## State Sync
```
sudo systemctl stop vidulumd

cp $HOME/.vidulum/data/priv_validator_state.json $HOME/.vidulum/priv_validator_state.json.backup
vidulumd tendermint unsafe-reset-all --home $HOME/.vidulum --keep-addr-book

SNAP_RPC="https://mainnet-vidulum-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="8c711d83224dc87cc516c92697f9124518fee542@mainnet-vidulum.konsortech.xyz:20656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.vidulum/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.vidulum/config/config.toml

mv $HOME/.vidulum/priv_validator_state.json.backup $HOME/.vidulum/data/priv_validator_state.json

sudo systemctl restart vidulumd
sudo journalctl -u vidulumd -f --no-hostname -o cat
```
