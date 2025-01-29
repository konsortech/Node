## State Sync
```
sudo systemctl stop atomoned

cp $HOME/.atomone/data/priv_validator_state.json $HOME/.atomone/priv_validator_state.json.backup
atomoned tendermint unsafe-reset-all --home $HOME/.atomone --keep-addr-book

SNAP_RPC="https://mainnet-atomone-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="13745b16a6e037d9282c7d77980f05f20c4cbd41@mainnet-atomone.konsortech.xyz:12656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.atomone/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.atomone/config/config.toml

mv $HOME/.atomone/priv_validator_state.json.backup $HOME/.atomone/data/priv_validator_state.json

sudo systemctl restart atomoned
sudo journalctl -u atomoned -f --no-hostname -o cat
```
