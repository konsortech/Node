sudo systemctl stop mund
cp $HOME/.mun/data/priv_validator_state.json $HOME/.mun/priv_validator_state.json.backup
rm -rf $HOME/.mun/data

SNAP_NAME=$(curl -s https://snapshot3.konsortech.xyz/mun/ | egrep -o ">mun-snapshot.*\.tar.lz4" | tr -d ">")
curl https://snapshot3.konsortech.xyz/mun/${SNAP_NAME} | lz4 -dc - | tar -xf - -C $HOME/.mun
mv $HOME/.mun/priv_validator_state.json.backup $HOME/.mun/data/priv_validator_state.json

sudo systemctl restart mund && journalctl -u mund -f --no-hostname -o cat
