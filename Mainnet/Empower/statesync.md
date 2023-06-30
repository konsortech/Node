sudo systemctl stop empowerd

cp $HOME/.empowerchain/data/priv_validator_state.json $HOME/.empowerchain/priv_validator_state.json.backup
empowerd tendermint unsafe-reset-all --home $HOME/.empowerchain --keep-addr-book

SNAP_RPC="https://mainnet-empower-rpc.konsortech.xyz:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

peers="c4277e884bbbf33ef1d8f13cbf26adb3b12336fd@mainnet-empower.konsortech.xyz:27656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.empowerchain/config/config.toml

sed -i -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.empowerchain/config/config.toml

mv $HOME/.empowerchain/priv_validator_state.json.backup $HOME/.empowerchain/data/priv_validator_state.json

sudo systemctl restart empowerd
sudo journalctl -u empowerd -f --no-hostname -o cat
