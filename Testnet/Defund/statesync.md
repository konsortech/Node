## State Sync
```
sudo systemctl stop defundd

cp $HOME/.defund/data/priv_validator_state.json $HOME/.defund/priv_validator_state.json.backup
defundd tendermint unsafe-reset-all --home $HOME/.defund --keep-addr-book

SNAP_RPC="https://testnet-defund-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="37f09319c05cfc92d250947f0ae3e1f7b609df16@testnet-defund.konsortech.xyz:26656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.defund/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.defund/config/config.toml

mv $HOME/.defund/priv_validator_state.json.backup $HOME/.defund/data/priv_validator_state.json

sudo systemctl restart defundd
sudo journalctl -u defundd -f --no-hostname -o cat
```
