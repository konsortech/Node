# install dependencies
```
sudo apt install curl build-essential git wget jq make gcc tmux chrony -y
```
# Download installer
```
curl -sSL https://raw.githubusercontent.com/terra-money/alliance/feat/wasm-testnets/scripts/join-testnet.sh -O 
chmod 755 ./join-testnet.sh
```
# running and select chain
```
./join-testnet.sh -c atreides-1
```
# copy go path
```
sudo cp ~/go/bin/atreidesd /usr/local/bin/atreidesd
```
# setup wallet
```
atreidesd config keyring-backend test 
```
# add wallet
```
atreidesd keys add wallet --recover
```

# running chain after running stop the node CTRL + C
```
atreidesd start --p2p.persistent_peers cd19f4418b3cd10951060aad1c4b4baf82177292@35.168.16.221:41456,1f4f91485f348dcca76fdf2cc3c0c16db6dee7ff@52.91.39.40:41456,36b2547e91dbaa1a6196217f25b767a8630fb0b2@54.196.186.174:41456
```
# create service
```
sudo tee /etc/systemd/system/atreidesd.service > /dev/null <<EOF
[Unit]
Description=atreides
After=network-online.target

[Service]
User=$USER
ExecStart=$(which atreidesd) start --home $HOME/.atreides
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

#create service
```
sudo systemctl daemon-reload
sudo systemctl enable atreidesd
sudo systemctl restart atreidesd && sudo journalctl -u atreidesd -f -o cat
```
#check status blcok
```
atreidesd status 2>&1 | jq .SyncInfo
```

#check wallet balance 
```
atreidesd query bank balances YourAddress
```
