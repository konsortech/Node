### How To Install Full Node Ollo Testnet

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
echo "export OLLO_CHAIN_ID=ollo-testnet-1" >> $HOME/.bash_profile
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
git clone https://github.com/OLLO-Station/ollo.git
cd ollo
git checkout v0.0.1
make install
```

## Config app
```
ollod config chain-id $OLLO_CHAIN_ID
ollod config keyring-backend test
```

## Init app
```
ollod init $NODENAME --chain-id $OLLO_CHAIN_ID
```

### Download configuration
```
wget -qO $HOME/.ollo/config/genesis.json "https://raw.githubusercontent.com/OllO-Station/networks/master/ollo-testnet-1/genesis.json"
wget -qO $HOME/.ollo/config/addrbook.json "https://raw.githubusercontent.com/konsortech/Node/main/Testnet/Ollo/addrbook.json"
```



## Set seeds and peers
```
SEEDS=""
PEERS="a99fc4e81770ca32d574cac2e8680dccc9b55f74@18.144.61.148:26656,70ba32724461c7ed4ec8d6ddc8b5e0b1cfb9e237@54.219.57.63:26656,7864a2e4b42e5af76a83a8b644b9172fa1e40fa5@52.8.174.235:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.ollo/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.ollo/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.ollo/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.ollo/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.ollo/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.ollo/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0utollo\"/" $HOME/.ollo/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/ollod.service > /dev/null <<EOF
[Unit]
Description=ollo
After=network-online.target

[Service]
User=$USER
ExecStart=$(which ollod) start --home $HOME/.ollo
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
sudo systemctl enable ollod
sudo systemctl restart ollod && sudo journalctl -u ollod -f -o cat
```
