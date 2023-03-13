### How To Install Full Node Arkhadian Mainnet

## Setting up vars
Your Nodename (validator) that will shows in explorer
```
NODENAME=<Your_Nodename_Moniker>
ARKHADIAN_PORT=30
```

Save variables to system
```
echo "export NODENAME=$NODENAME" >> $HOME/.bash_profile
if [ ! $WALLET ]; then
	echo "export WALLET=wallet" >> $HOME/.bash_profile
fi
echo "export ARKHADIAN_CHAIN_ID=arkh" >> $HOME/.bash_profile
echo "export ARKHADIAN_PORT=${ARKHADIAN_PORT}" >> $HOME/.bash_profile
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

## Download and build binaries
```
cd $HOME
git clone https://github.com/vincadian/arkh-blockchain
cd arkh-blockchain
git checkout v2.0.0
go build -o arkh ./cmd/arkhd
sudo mv arkh /usr/bin/
```

## Config app
```
arkh config chain-id $ARKHADIAN_CHAIN_ID
arkh config keyring-backend test
arkh config node tcp://localhost:30657
```

## Init app
```
arkh  init $NODENAME --chain-id $ARKHADIAN_CHAIN_ID
```

### Download configuration
```
wget https://raw.githubusercontent.com/konsortech/Node/main/Mainnet/Arkhadian/genesis.json -O $HOME/.arkh/config/genesis.json
wget https://raw.githubusercontent.com/konsortech/Node/main/Mainnet/Arkhadian/addrbook.json -O $HOME/.arkh/config/addrbook.json
```

## Set seeds and peers
```
sed -i -e "s|^seeds *=.*|seeds = \"808f01d4a7507bf7478027a08d95c575e1b5fa3c@asc-dataseed.arkhadian.com:26656\"|" $HOME/.arkh/config/config.toml
```

## Set minimum gas price
```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025arkh\"/" $HOME/.arkh/config/app.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.arkh/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.arkh/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.arkh/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.arkh/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/arkh.service > /dev/null << EOF
[Unit]
Description=Arkh Node
After=network-online.target

[Service]
User=root
ExecStart=/usr/bin/arkh start --home /root/.arkh
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
sudo systemctl enable arkh
sudo systemctl restart arkh && sudo journalctl -u arkh -f -o cat
```
