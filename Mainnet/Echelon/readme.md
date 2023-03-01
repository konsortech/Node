### How To Install Full Node echelond MAINNET



## Setting up vars
Your Nodename (validator) that will shows in explorer
```
NODENAME=<Your_Nodename_Moniker>
ECHELON_PORT=12
```

Save variables to system
```
echo "export NODENAME=$NODENAME" >> $HOME/.bash_profile
if [ ! $WALLET ]; then
	echo "export WALLET=wallet" >> $HOME/.bash_profile
fi
echo "export ECHELON_CHAIN_ID=echelon_3000-3" >> $HOME/.bash_profile
echo "export ECHELON_PORT=${ECHELON_PORT}" >> $HOME/.bash_profile
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
git clone https://github.com/echelonfoundation/echelon 
cd echelon 
make install 
sudo cp ~/go/bin/echelond /usr/local/bin/echelond
```

## Config app
```
echelond config chain-id $ECHELON_CHAIN_ID
echelond config keyring-backend file
echelond config node tcp://localhost:12657
```
## set custom ports
```
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${ECHELON_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${ECHELON_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${ECHELON_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${ECHELON_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${ECHELON_PORT}660\"%" $HOME/.echelond/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${ECHELON_PORT}317\"%; s%^address = \":8080\"%address = \":${ECHELON_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${ECHELON_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${ECHELON_PORT}091\"%" $HOME/.echelond/config/app.toml
```

## Init app
```
echelond init $NODENAME --chain-id $ECHELON_CHAIN_ID
```

### Download configuration
```
cd $HOME
curl https://gist.githubusercontent.com/echelonfoundation/ee862f58850fc1b5ee6a6fdccc3130d2/raw/55c2c4ea2fee8a9391d0dc55b2c272adb804054a/genesis.json > ~/.echelond/config/genesis.json
wget -O $HOME/.echelond/config/addrbook.json https://ech.world/latest/addrbook.json
```

## Set seeds and peers
```
sed -i -e "s|^seeds *=.*|seeds = \"ab8febad726c213fac69361c8fd47adc3f302e64@38.242.143.4:26656,fda4d1c914a667e72181839fcfddb238c7e480c8@85.239.240.101:26656\"|" $HOME/.echelond/config/config.toml

```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.echelond/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.echelond/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.echelond/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.echelond/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.echelond/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.0125aechelon\"|" $HOME/.echelond/config/app.toml

```

## Create service
```
sudo tee /etc/systemd/system/echelond.service > /dev/null <<EOF
[Unit]
Description=echelon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which echelond) start --home $HOME/.echelond
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
sudo systemctl enable echelond
sudo systemctl restart echelond && sudo journalctl -u echelond -f -o cat
```
