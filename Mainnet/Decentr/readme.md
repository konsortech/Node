

### How To Install Full Node DECENTR MAINNET
## Setting up vars
Your Nodename (validator) that will shows in explorer
```
NODENAME=<Your_Nodename_Moniker>
DECENTR_PORT=32
```

Save variables to system
```
echo "export NODENAME=$NODENAME" >> $HOME/.bash_profile
if [ ! $WALLET ]; then
	echo "export WALLET=wallet" >> $HOME/.bash_profile
fi
echo "export DECENTR_CHAIN_ID=mainnet-3" >> $HOME/.bash_profile
echo "export DECENTR_PORT=${DECENTR_PORT}" >> $HOME/.bash_profile
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
cd $HOME && rm -rf decentr
git clone https://github.com/Decentr-net/decentr.git && cd decentr
git checkout v1.6.2
make install
```

## Config app
```
decentrd config chain-id $DECENTR_CHAIN_ID
decentrd config node tcp://localhost:32657
```
## set custom ports
```
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${DECENTR_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${DECENTR_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${DECENTR_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${DECENTR_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${DECENTR_PORT}660\"%" $HOME/.decentr/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${DECENTR_PORT}317\"%; s%^address = \":8080\"%address = \":${DECENTR_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${DECENTR_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${DECENTR_PORT}091\"%" $HOME/.decentr/config/app.toml
```

## Init app
```
decentrd init $NODENAME --chain-id $DECENTR_CHAIN_ID
```

### Download configuration
```
cd $HOME
curl -Ls https://raw.githubusercontent.com/konsortech/Node/main/Mainnet/Decentr/genesis.json > $HOME/.decentr/config/genesis.json
curl -Ls https://raw.githubusercontent.com/konsortech/Node/main/Mainnet/Decentr/addrbook.json > $HOME/.decentr/config/addrbook.json
```

## Set seeds and peers
```
sed -E -i 's/seeds = \".*\"/seeds = \"7708addcfb9d4ff394b18fbc6c016b4aaa90a10a@ares.mainnet.decentr.xyz:26656,8a3485f940c3b2b9f0dd979a16ea28de154f14dd@calliope.mainnet.decentr.xyz:26656,87490fd832f3226ac5d090f6a438d402670881d0@euterpe.mainnet.decentr.xyz:26656,3261bff0b7c16dcf6b5b8e62dd54faafbfd75415@hera.mainnet.decentr.xyz:26656,5f3cfa2e3d5ed2c2ef699c8593a3d93c902406a9@hermes.mainnet.decentr.xyz:26656,a529801b5390f56d5c280eaff4ae95b7163e385f@melpomene.mainnet.decentr.xyz:26656,385129dbe71bceff982204afa11ed7fa0ee39430@poseidon.mainnet.decentr.xyz:26656,35a934228c32ad8329ac917613a25474cc79bc08@terpsichore.mainnet.decentr.xyz:26656,0fd62bcd1de6f2e3cfc15852cdde9f3f8a7987e4@thalia.mainnet.decentr.xyz:26656,bd99693d0dbc855b0367f781fb48bf1ca6a6a58b@zeus.mainnet.decentr.xyz:26656\"/' $HOME/.decentr/config/config.toml


```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.decentr/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.decentr/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.decentr/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.decentr/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.decentr/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.025udec\"|" $HOME/.decentr/config/app.toml

```

## Create service
```
sudo tee /etc/systemd/system/decentrd.service > /dev/null <<EOF
[Unit]
Description=Decentr Network Node
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which decentrd) start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

## Register and start service
```
sudo systemctl daemon-reload
sudo systemctl enable decentrd
sudo systemctl restart decentrd && sudo journalctl -u decentrd -f -o cat
```
