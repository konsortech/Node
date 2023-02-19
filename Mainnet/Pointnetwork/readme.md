### How To Install Full Node Pointnetwork Mainnet

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
echo "export POINT_CHAIN_ID=point_10687-1" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

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
  ver="1.18.2"
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
git clone https://github.com/pointnetwork/point-chain
cd point-chain
git checkout mainnet
make install
```

## Config app
```
pointd config chain-id $POINT_CHAIN_ID
```

## Init app
```
pointd init $NODENAME --chain-id $POINT_CHAIN_ID
```

### Download configuration
```
wget -O $HOME/.pointd/config/genesis.json "https://raw.githubusercontent.com/pointnetwork/point-chain-config/main/mainnet-1/genesis.json"
wget -O $HOME/.pointd/config/config.toml https://raw.githubusercontent.com/pointnetwork/point-chain-config/main/mainnet-1/config.toml
```

## Set seeds and peers
```
PEERS='curl -s https://raw.githubusercontent.com/pointnetwork/point-chain-config/main/mainnet-1/peers.txt'
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.pointd/config/config.toml
```

## Disable indexing
```
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.pointd/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.pointd/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.pointd/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.pointd/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.pointd/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/pointd.service > /dev/null <<EOF
[Unit]
Description=point
After=network-online.target

[Service]
User=$USER
ExecStart=$(which pointd) start --home $HOME/.pointd
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
sudo systemctl enable pointd
sudo systemctl restart pointdd && sudo journalctl -u pointd -f -o cat
```
