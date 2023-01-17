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

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.iris/config/config.toml
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
