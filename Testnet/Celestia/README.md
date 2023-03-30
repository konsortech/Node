# Guide Setup light node celestia


## Update packages
```
sudo apt update && sudo apt upgrade -y
```

## Install dependencies
```
sudo apt install curl build-essential git wget jq make gcc tmux net-tools ccze -y
```

## Install go
```
if ! [ -x "$(command -v go)" ]; then
  ver="1.19.4"
  cd $HOME
  wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
  sudo rm -rf /usr/local/go
  sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
  rm "go$ver.linux-amd64.tar.gz"
  echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
  source ~/.bash_profile
fi
```

# Download and build binaries 
```
cd $HOME 
rm -rf celestia-node 
git clone https://github.com/celestiaorg/celestia-node.git 
cd celestia-node/ 
git checkout tags/v0.8.0 
make build 
make install 
make cel-key 
```
# Create new wallet key and Save the mnemonic key DONT LOSE!!!!
```
./cel-key add wallet --keyring-backend test --node.type light --p2p.network blockspacerace
```
# restore old key 
```
./cel-key add wallet --keyring-backend test --node.type light --p2p.network blockspacerace --recover
```
# request faucet on celestia discord 
```
https://discord.gg/celestiacommunity
```
# Initialize Light node
```
celestia light init \
  --keyring.accname wallet \
  --p2p.network blockspacerace
```
# create service for celestia light node 
```
sudo tee <<EOF >/dev/null /etc/systemd/system/celestia-lightd.service
[Unit]
Description=celestia-lightd Light Node
After=network-online.target

[Service]
User=$USER
ExecStart=/usr/local/bin/celestia light start --core.ip https://rpc-blockspacerace.pops.one --core.rpc.port 26657 --core.grpc.port 9090 --keyring.accname wallet --metrics.tls=false --metrics --metrics.endpoint otel.celestia.tools:4318 --gateway --gateway.addr localhost --gateway.port 26659 --p2p.network blockspacerace
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF

```
# lets start the node 
```
sudo systemctl daemon-reload
sudo systemctl enable celestia-lightd
sudo systemctl restart celestia-lightd && sudo journalctl -u celestia-lightd.service -f
```

