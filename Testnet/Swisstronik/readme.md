### How To Install Full Node Swisstronik Testnet

## Official Docs
```
https://swisstronik.gitbook.io/swisstronik-docs
```

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
echo "export SWISSTRONIK_CHAIN_ID=swisstronik_1291-1" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Update packages
```
sudo apt update && sudo apt upgrade -y
```

## Install dependencies
```
sudo apt install curl build-essential git wget jq make gcc tmux net-tools ccze unzip lz4 -y
```

## Install go
```
if ! [ -x "$(command -v go)" ]; then
  ver="1.21.4"
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
wget https://github.com/SigmaGmbH/swisstronik-chain/releases/download/v1.0.1/swisstronikd.deb.zip 
unzip swisstronikd.deb.zip  
dpkg -i swisstronik_1.0.1-updated-binaries_amd64.deb
swisstronikd version
```

## Init app
```
swisstronikd init $NODENAME --chain-id $SWISSTRONIK_CHAIN_ID
```

### Download configuration
```
wget -O $HOME/.swisstronik/config/genesis.json "https://snap.konsortech.xyz/swisstornik/genesis.json"
wget -O $HOME/.swisstronik/config/addrbook.json "https://snap.konsortech.xyz/swisstornik/addrbook.json"
```

## Set the minimum gas price and Peers, Filter peers/ MaxPeers
```
SEEDS="54b1092174218aad93a22f7ac2eaa74bb2326bbc@testnet-seed.konsortech.xyz:29165"
PEERS="7c42a55317deda257fee06bc48574fa3444967db@testnet-swisstronik.konsortech.xyz:27656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.swisstronik/config/config.toml
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"7uswtr\"/;" ~/.swisstronik/config/app.toml
external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.swisstronik/config/config.toml
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.swisstronik/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.swisstronik/config/config.toml
```

## Config pruning
```
pruning="custom" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="10" && \
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" ~/.swisstronik/config/app.toml && \
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" ~/.swisstronik/config/app.toml && \
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" ~/.swisstronik/config/app.toml && \
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" ~/.swisstronik/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/swisstronikd.service > /dev/null <<EOF
[Unit]
Description=Swisstronik
After=network-online.target

[Service]
User=$USER
ExecStart=$(which swisstronikd) start
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
sudo systemctl enable swisstronikd
sudo systemctl restart swisstronikd && sudo journalctl -u swisstronikd -f -o cat
```
