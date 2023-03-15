## State Sync
```
sudo systemctl stop decentrd

cp $HOME/.decentr/data/priv_validator_state.json $HOME/.decentr/priv_validator_state.json.backup
decentrd tendermint unsafe-reset-all --home $HOME/.decentr --keep-addr-book

SNAP_RPC="https://mainnet-decentr-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="f70a98de076a3a1c26ef34f2e42490cff826852f@mainnet-decentr.konsortech.xyz"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.decentr/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.decentr/config/config.toml

mv $HOME/.decentr/priv_validator_state.json.backup $HOME/.decentr/data/priv_validator_state.json

sudo systemctl restart decentrd
sudo journalctl -u decentrd -f --no-hostname -o cat
```
