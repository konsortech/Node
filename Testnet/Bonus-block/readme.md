### How To Install Full Node Bonus-Block testnet



## Setting up vars
Your Nodename (validator) that will shows in explorer
```
NODENAME=<Your_Nodename_Moniker>
BONUSBLOCKPORT=15
```

Save variables to system
```
echo "export NODENAME=$NODENAME" >> $HOME/.bash_profile
if [ ! $WALLET ]; then
	echo "export WALLET=wallet" >> $HOME/.bash_profile
fi
echo "export BONUSBLOCK_CHAIN_ID=blocktopia-01" >> $HOME/.bash_profile
echo "export BONUSBLOCK_PORT=${BONUSBLOCKPORT}" >> $HOME/.bash_profile
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
git clone https://github.com/BBlockLabs/BonusBlock-chain
cd BonusBlock-chain
make build
```

## Config app
```
bonus-blockd config chain-id $BONUSBLOCKCHAIN_ID
bonus-blockd config keyring-backend test
bonus-blockd config node tcp://localhost:15657
```
## set custom ports
```
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${BONUSBLOCKPORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${BONUSBLOCKPORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${BONUSBLOCKPORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${BONUSBLOCKPORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${BONUSBLOCKPORT}660\"%" $HOME/.bonusblock/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${BONUSBLOCKPORT}317\"%; s%^address = \":8080\"%address = \":${BONUSBLOCKPORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${BONUSBLOCKPORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${BONUSBLOCKPORT}091\"%" $HOME/.bonusblock/config/app.toml
```

## Init app
```
bonus-blockd init $NODENAME --chain-id $BONUSBLOCKCHAIN_ID
```

### Download configuration
```
cd $HOME
curl -Ls https://raw.githubusercontent.com/konsortech/Node/blob/main/Testnet/Bonus-block/addrbook.json > $HOME/.bonusblock/config/genesis.json
curl -Ls https://raw.githubusercontent.com/konsortech/Node/blob/main/Testnet/Bonus-block/genesis.json > $HOME/.bonusblock/config/addrbook.json
```

## Set seeds and peers
```
PEERS=e5e04918240cfe63e20059a8abcbe62f7eb05036@bonusblock-testnet-p2p.alter.network:26656
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.bonusblock/config/config.toml


```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.bonusblock/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.bonusblock/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.bonusblock/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.bonusblock/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.bonusblock/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.025ubonus\"|" $HOME/.bonusblock/config/app.toml

```

## Create service
```
sudo tee /etc/systemd/system/bonus-blockd.service > /dev/null <<EOF
[Unit]
Description=Bonus block  Node
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which bonus-blockd) start
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
sudo systemctl enable bonus-blockd
sudo systemctl restart bonus-blockd && sudo journalctl -u bonus-blockd -f -o cat
```
