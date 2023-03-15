### How To Install Full Node KYVE MAINNET



## Setting up vars
Your Nodename (validator) that will shows in explorer
```
NODENAME=<Your_Nodename_Moniker>
KYVE_PORT=31
```

Save variables to system
```
echo "export NODENAME=$NODENAME" >> $HOME/.bash_profile
if [ ! $WALLET ]; then
	echo "export WALLET=wallet" >> $HOME/.bash_profile
fi
echo "export KYVE_CHAIN_ID=kyve-1" >> $HOME/.bash_profile
echo "export KYVE_PORT=${KYVE_PORT}" >> $HOME/.bash_profile
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
cd $HOME && rm -rf chain
git clone https://github.com/KYVENetwork/chain.git && cd chain
git checkout v1.0.0
make install
```

## Config app
```
kyved config chain-id $KYVE_CHAIN_ID
kyved config keyring-backend test
kyved config node tcp://localhost:31657
```
## set custom ports
```
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${KYVE_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${KYVE_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${KYVE_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${KYVE_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${KYVE_PORT}660\"%" $HOME/.kyve/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${KYVE_PORT}317\"%; s%^address = \":8080\"%address = \":${KYVE_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${KYVE_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${KYVE_PORT}091\"%" $HOME/.kyve/config/app.toml
```

## Init app
```
kyved init $NODENAME --chain-id $KYVE_CHAIN_ID
```

### Download configuration
```
cd $HOME
curl -Ls https://raw.githubusercontent.com/konsortech/Node/main/Mainnet/KYVE/genesis.json > $HOME/.kyve/config/genesis.json
curl -Ls https://raw.githubusercontent.com/konsortech/Node/main/Mainnet/KYVE/addrbook.json > $HOME/.kyve/config/addrbook.json
```

## Set seeds and peers
```
SEEDS="4a72671447a1ede2a136dbac70ac4523c3433c94@seeds.cros-nest.com:29656"
PEERS="b950b6b08f7a6d5c3e068fcd263802b336ffe047@18.198.182.214:26656,25da6253fc8740893277630461eb34c2e4daf545@3.76.244.30:26656,146d27829fd240e0e4672700514e9835cb6fdd98@34.212.201.1:26656,fae8cd5f04406e64484a7a8b6719eacbb861c094@44.241.103.199:26656,443f41172aafaa6c711333c621e019fde3f0ba99@5.75.144.137:26656,cfb5d3dc65e8e1d17285964655d2b47a44d35721@144.76.97.251:42656,c782ab00baf1c86261db0570307a9ecd9c5b197a@5.9.63.216:28656,38ef1bd70583c8831c3c0e07fad7e1bab56515cc@68.183.143.17:26656,307f4024107ef114dba355fe97dab44b8b45cefc@38.242.253.58:29656,86d313c22789ffa50c76b85b460f1e1412782a27@195.3.221.59:12656,a0ba3bd9616b51c26ab6ecc49a30a13d0438ab7f@65.109.94.250:28656,cec6c3c59d1bde0862d27500bf3c0ecc39b4727d@3.144.87.60:31309,0ab23bfd2924c09a0cb2166a78e65d6d0fbd172a@57.128.162.152:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.kyve/config/config.toml


```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.kyve/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.kyve/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.kyve/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.kyve/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.kyve/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.025ukyve\"|" $HOME/.kyve/config/app.toml

```

## Create service
```
sudo tee /etc/systemd/system/kyved.service > /dev/null <<EOF
[Unit]
Description=Kyve Network Node
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which kyved) start
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
sudo systemctl enable kyved
sudo systemctl restart kyved && sudo journalctl -u kyved -f -o cat
```
