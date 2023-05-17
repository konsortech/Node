### How To Install Full Node Bitcanna Mainnet

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
echo "export BITCANNA_CHAIN_ID=bitcanna-1" >> $HOME/.bash_profile
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
git clone https://github.com/BitCannaGlobal/bcna.git
cd bcna
git checkout v1.5.3
make install
```

## Config app
```
bcnad config chain-id $BITCANNA_CHAIN_ID
```

## Init app
```
bcnad init $NODENAME --chain-id $BITCANNA_CHAIN_ID
```

### Download configuration
```
curl https://raw.githubusercontent.com/BitCannaGlobal/bcna/main/genesis.json > $HOME/.bcna/config/genesis.json
curl https://raw.githubusercontent.com/konsortech/Node/main/Mainnet/Bitcanna/addrbook.json > $HOME/.bcna/config/addrbook.json
```

## Set seeds and peers
```
seeds="d6aa4c9f3ccecb0cc52109a95962b4618d69dd3f@seed1.bitcanna.io:26656,23671067d0fd40aec523290585c7d8e91034a771@seed2.bitcanna.io:26656"
peers="5a048cab1d183de5c465c56b29a16fd93a8bf9bd@mainnet-bitcanna.konsortech.xyz:27656"
sed -i -e 's|^seeds *=.*|seeds = "'$seeds'"|; s|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.bcna/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.bcna/config/config.toml
```

## Config pruning
```
sed -i 's|pruning = "default"|pruning = "custom"|g' $HOME/.bcna/config/app.toml
sed -i 's|pruning-keep-recent = "0"|pruning-keep-recent = "100"|g' $HOME/.bcna/config/app.toml
sed -i 's|pruning-interval = "0"|pruning-interval = "17"|g' $HOME/.bcna/config/app.toml
```

## Set minimum gas price
```
sed -i 's|^minimum-gas-prices *=.*|minimum-gas-prices = "0.0001ubcna"|g' $HOME/.bcna/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/bcnad.service > /dev/null << EOF
[Unit]
Description=Bitcanna Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which bcnad) start
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
sudo systemctl enable bcnad
sudo systemctl restart bcnad && sudo journalctl -u bcnad -f -o cat
```
