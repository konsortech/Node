### How To Install Full Node TimpiTN Network Testnet

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
echo "export TIMPI_CHAIN_ID=TimpiChainTN2" >> $HOME/.bash_profile
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
git clone https://github.com/Timpi-official/Timpi-ChainTN.git
cd Timpi-ChainTN
cd cmd/TimpiChain
go build
mv TimpiChain $HOME/go/bin/timpid
```

## Config app
```
timpid config chain-id $TIMPI_CHAIN_ID
timpid config keyring-backend test
```

## Init app
```
timpid init $NODENAME --chain-id $TIMPI_CHAIN_ID
```

### Download configuration
```
cd $HOME
curl -Ls http://173.249.54.208/genesis.json > $HOME/.TimpiChain/config/genesis.json
```

## Set seeds and peers
```
peers="16700793659365235701335a41dd7b2b317518dd@173.249.54.208:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.TimpiChain/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.TimpiChain/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.TimpiChain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.TimpiChain/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.TimpiChain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.TimpiChain/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0utimpiTN\"|" $HOME/.TimpiChain/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/timpid.service > /dev/null <<EOF
[Unit]
Description=TimpiTN
After=network-online.target

[Service]
User=$USER
ExecStart=$(which timpid) start
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
sudo systemctl enable timpid
sudo systemctl restart timpid && sudo journalctl -u timpid -f -o cat
```
