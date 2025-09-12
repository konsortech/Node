### How To Install Full Node Paxi Mainnet

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
echo "export PAXI_CHAIN_ID=paxi-mainnet" >> $HOME/.bash_profile
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
  ver="1.21.2"
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
git clone https://github.com/paxi-web3/paxi.git
cd paxi
git checkout v1.0.5
make install
cp ~/paxid/paxid ~/go/bin
```

## Init app
```
paxid init $NODENAME --chain-id $PAXI_CHAIN_ID
```

### Download configuration
```
wget https://snap1.konsortech.xyz/paxi/genesis.json -O ~/go/bin/paxi/config/genesis.json
wget https://snap1.konsortech.xyz/paxi/addrbook.json -O ~/go/bin/paxi/config/addrbook.json
```

## Set the minimum gas price and Peers, Filter peers/ MaxPeers
```
SEEDS="@mainnet-seed.konsortech.xyz:"
PEERS="@mainnet-paxi.konsortech.xyz:"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" ~/go/bin/paxi/config/config.toml
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.01upaxi\"/" ~/go/bin/paxi/config/app.toml
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" ~/go/bin/paxi/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" ~/go/bin/paxi/config/config.toml
```

## Config pruning
```
pruning="custom" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="10" && \
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" ~/go/bin/paxi/config/app.toml && \
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" ~/go/bin/paxi/config/app.toml && \
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" ~/go/bin/paxi/config/app.toml && \
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" ~/go/bin/paxi/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/paxid.service > /dev/null <<EOF
[Unit]
Description=Paxi Node
After=network-online.target

[Service]
User=$USER
ExecStart=$(which paxid) start
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
sudo systemctl enable paxid
sudo systemctl restart paxid && sudo journalctl -u paxid -f -o cat
```
