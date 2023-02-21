### How To Install Full Node Andromeda Testnet



## Setting up vars
Your Nodename (validator) that will shows in explorer
```
NODENAME=<Your_Nodename_Moniker>
EIGHTBALL_PORT=18
```

Save variables to system
```
echo "export NODENAME=$NODENAME" >> $HOME/.bash_profile
if [ ! $WALLET ]; then
	echo "export WALLET=wallet" >> $HOME/.bash_profile
fi
echo "export EIGHTBALL_CHAIN_ID=eightball-1" >> $HOME/.bash_profile
echo "export EIGHTBALL_PORT=${EIGHTBALL_PORT}" >> $HOME/.bash_profile
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
git clone https://github.com/sxlmnwb/8ball.git
cd 8ball
git checkout v0.34.24
go build -o 8ball ./cmd/eightballd
sudo mv 8ball /usr/bin/
```

## Config app
```
8ball config chain-id $EIGHTBALL_CHAIN_ID
8ball config keyring-backend test
8ball config node tcp://localhost:18657
```
## set custom ports
```
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${EIGHTBALL_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${EIGHTBALL_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${EIGHTBALL_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${EIGHTBALL_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${EIGHTBALL_PORT}660\"%" $HOME/.8ball/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${EIGHTBALL_PORT}317\"%; s%^address = \":8080\"%address = \":${EIGHTBALL_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${EIGHTBALL_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${EIGHTBALL_PORT}091\"%" $HOME/.8ball/config/app.toml
```

## Init app
```
8ball init $NODENAME --chain-id $EIGHTBALL_CHAIN_ID
```

### Download configuration
```
cd $HOME
curl -Ls https://raw.githubusercontent.com/konsortech/Node/main/Mainnet/8ball/genesis.json > $HOME/.8ball/config/genesis.json
curl -Ls https://raw.githubusercontent.com/konsortech/Node/main/Mainnet/8ball/addrbook.json > $HOME/.8ball/config/addrbook.json
```

## Set seeds and peers
```
sed -i -e "s|^seeds *=.*|seeds = \"fca96d0a1d7357afb226a49c4c7d9126118c37e9@one.8ball.info:26656,aa918e17c8066cd3b031f490f0019c1a95afe7e3@two.8ball.info:26656,98b49fea92b266ed8cfb0154028c79f81d16a825@three.8ball.info:26656\"|" $HOME/.8ball/config/config.toml

```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.8ball/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.8ball/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.8ball/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.8ball/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.8ball/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.025uebl\"|" $HOME/.8ball/config/app.toml

```

## Create service
```
sudo tee /etc/systemd/system/8ball.service > /dev/null <<EOF
[Unit]
Description=eightball
After=network-online.target

[Service]
User=$USER
ExecStart=$(which 8ball) start --home $HOME/.8ball
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
sudo systemctl enable 8ball
sudo systemctl restart 8ball && sudo journalctl -u 8ball -f -o cat
```
