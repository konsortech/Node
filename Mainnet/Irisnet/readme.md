### How To Install Full Node IrisNet Mainnet

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
echo "export IRIS_CHAIN_ID=irishub-1" >> $HOME/.bash_profile
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
git clone https://github.com/irisnet/irishub
cd irishub
git checkout v1.4.1
make install
```

## Config app
```
iris config chain-id $IRIS_CHAIN_ID
```

## Init app
```
iris init $NODENAME --chain-id $IRIS_CHAIN_ID
```

### Download configuration
```
curl -o ~/.iris/config/config.toml https://raw.githubusercontent.com/irisnet/mainnet/master/config/config.toml
curl -o ~/.iris/config/genesis.json https://raw.githubusercontent.com/irisnet/mainnet/master/config/genesis.json
```

## Set seeds and peers
```
seeds="a17d7923293203c64ba75723db4d5f28e642f469@seed-2.mainnet.irisnet.org:26656"
peers="fdc0406afdd3acc63f74f5439e09104f663a7c1f@44.241.177.178:26656,090bcbe5302e6104821a96c4899912870db04cb9@52.11.128.123:26656,83b3f989f3ce089afdf733f8aa06e792d7e00c08@3.34.6.30:26656"
sed -i -e 's|^seeds *=.*|seeds = "'$seeds'"|; s|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.iris/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.iris/config/config.toml
```

## Config pruning
```
sed -i 's|pruning = "default"|pruning = "custom"|g' $HOME/.iris/config/app.toml
sed -i 's|pruning-keep-recent = "0"|pruning-keep-recent = "100"|g' $HOME/.iris/config/app.toml
sed -i 's|pruning-interval = "0"|pruning-interval = "17"|g' $HOME/.iris/config/app.toml
```

## Set minimum gas price
```
sed -i 's|^minimum-gas-prices *=.*|minimum-gas-prices = "0.2uiris"|g' $HOME/.iris/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/iris.service > /dev/null << EOF
[Unit]
Description=Bitcanna Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which iris) start
Restart=on-failure
RestartSec=10
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
EOF
```

## Register and start service
```
sudo systemctl daemon-reload
sudo systemctl enable iris
sudo systemctl restart iris && sudo journalctl -u iris -f -o cat
```
