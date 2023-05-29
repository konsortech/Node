### How To Install Full Node Humans Testnet

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
echo "export HUMANS_CHAIN_ID=humans_1089-1" >> $HOME/.bash_profile
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
  ver="1.20.2"
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
git clone https://github.com/humansdotai/humans
cd humans && git checkout tags/v1.0.0
make install
```

## Init app
```
humansd init $NODENAME --chain-id $HUMANS_CHAIN_ID
```

### Download configuration
```
wget https://raw.githubusercontent.com/humansdotai/mainnets/main/mainnet/1/genesis_1089-1.json -O $HOME/.humans/config/genesis.json
```

## Set the minimum gas price and Peers, Filter peers/ MaxPeers
```
SEEDS="f8006da7d742777eeca0194b94586c8f147be4f6@humans-mainnet-seed.itrocket.net:17656,babc3f3f7804933265ec9c40ad94f4da8e9e0017@seed.rhinostake.com:18456"
PEERS=""
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.humans/config/config.toml
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0aheart\"/;" ~/.humans/config/app.toml
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.humans/config/config.toml
external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.humans/config/config.toml
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.humans/config/config.toml
sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 100/g' $HOME/.humans/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 100/g' $HOME/.humans/config/config.toml

```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.humans/config/config.toml
```

## Config pruning
```
pruning="custom" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="10" && \
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" ~/.humans/config/app.toml && \
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" ~/.humans/config/app.toml && \
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" ~/.humans/config/app.toml && \
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" ~/.humans/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/humansd.service > /dev/null <<EOF
[Unit]
Description=humans
After=network-online.target

[Service]
User=$USER
ExecStart=$(which humansd) start
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
sudo systemctl enable humansd
sudo systemctl restart humansd && sudo journalctl -u humansd -f -o cat
```
