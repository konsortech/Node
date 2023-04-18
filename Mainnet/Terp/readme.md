### How To Install Full Node Terp Network Mainnet

## Setting up vars
Your Nodename (validator) that will shows in explorer
```
NODENAME=<Your_Nodename_Moniker>
```

Save variables to system
```
echo "export NODENAME=$NODENAME" >> $HOME/.bash_profile
if [ ! $WALLET ]; then
	echo "export WALLET=wallet" >> $HOME/.bash_profile
fi
echo "export TERP_CHAIN_ID=morocco-1" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Update packages
```
sudo apt update && sudo apt upgrade -y
```

## Install dependencies
```
sudo apt install curl build-essential git wget jq make gcc tmux net-tools -y
```

## Install go
```
if ! [ -x "$(command -v go)" ]; then
     ver="1.20.3"
     cd $HOME
     wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
     sudo rm -rf /usr/local/go
     sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
     rm "go$ver.linux-amd64.tar.gz"
     echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
     source ~/.bash_profile
fi
```

## Download and build binaries
```
cd $HOME
git clone https://github.com/terpnetwork/terp-core.git
cd terp-core
git checkout v1.0.0
make install
```

## Config app
```
terpd config chain-id $TERP_CHAIN_ID
```

## Init app
```
terpd  init $NODENAME --chain-id $TERP_CHAIN_ID
```

### Download configuration
```
wget -O $HOME/.terp/config/genesis.json https://mainnet-files.itrocket.net/terp/genesis.json
wget -O $HOME/.terp/config/addrbook.json https://mainnet-files.itrocket.net/terp/addrbook.json
```

## Set seeds and peers
```
SEEDS="d8256642afae77264bcce1631d51233a9d00249b@terp-mainnet-seed.itrocket.net:13656"
PEERS="a81dc3bf1bb1c3837b768eeb82659eecc971890b@terp-mainnet-peer.itrocket.net:13656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.terp/config/config.toml
```

## Disable indexing and set gas-prices
```
sed -i 's/minimum-gas-prices =.*/minimum-gas-prices = "0.0uterp"/g' $HOME/.terp/config/app.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.terp/config/config.toml
```

## Config pruning
```
sed -i -e "s/^pruning *=.*/pruning = \"nothing\"/" $HOME/.terp/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.terp/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.terp/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/terpd.service > /dev/null <<EOF
[Unit]
Description=Terp node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which terpd) start --home $HOME/.terp
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

## Register and start service
```
sudo systemctl daemon-reload
sudo systemctl enable terpd
sudo systemctl restart terpd && sudo journalctl -u terpd -f -o cat
```
