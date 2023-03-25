<p align="center">
  <img height="100" height="auto" src="https://user-images.githubusercontent.com/50621007/171044333-016e348d-1d96-4d00-8dce-f7de45aa9f84.png">
</p>

### How To Install Full Node Uptick Testnet

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
echo "export UPTICK_CHAIN_ID=uptick_117-1" >> $HOME/.bash_profile
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
git clone https://github.com/UptickNetwork/uptick.git
cd uptick
git checkout v0.2.4
make install
```

## Config app
```
uptickd config chain-id $UPTICK_CHAIN_ID
```

## Init app
```
uptickd init $NODENAME --chain-id $UPTICK_CHAIN_ID
```

### Download configuration
```
curl -o $HOME/.uptickd/config/genesis.json https://raw.githubusercontent.com/UptickNetwork/uptick-mainnet/master/uptick_117-1/genesis.json
```

## Set seeds and peers
```
SEEDS='ff97a75fb69d3a5fe893dca7c8d238ccc0bd66a8f@uptick.seed.brocha.in:30600'
PEERS=''
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.uptickd/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.uptickd/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.uptickd/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.uptickd/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.uptickd/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.uptickd/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0auptick\"/" $HOME/.uptickd/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/uptickd.service > /dev/null <<EOF
[Unit]
Description=uptick
After=network-online.target

[Service]
User=$USER
ExecStart=$(which uptickd) start --home $HOME/.uptickd
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
sudo systemctl enable uptickd
sudo systemctl restart uptickd && sudo journalctl -u uptickd -f -o cat
```
