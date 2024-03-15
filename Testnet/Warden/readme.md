### How To Install Full Node Warden Protocol Testnet

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
echo "export WARDEN_CHAIN_ID=alfama" >> $HOME/.bash_profile
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
  ver="1.21.4"
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
git clone --depth 1 --branch v0.1.0 https://github.com/warden-protocol/wardenprotocol/
cd wardenprotocol/warden/cmd/wardend
go build
sudo mv wardend /usr/local/bin/

```

## Config app
```
wardend config chain-id $WARDEN_CHAIN_ID
```

## Init app
```
wardend init $NODENAME --chain-id $WARDEN_CHAIN_ID
```

### Download configuration
```
cd $HOME/.warden/config
rm genesis.json
wget https://raw.githubusercontent.com/warden-protocol/networks/main/testnet-alfama/genesis.json
```

## Set seeds and peers
```
PEERS=6a8de92a3bb422c10f764fe8b0ab32e1e334d0bd@sentry-1.alfama.wardenprotocol.org:26656,7560460b016ee0867cae5642adace5d011c6c0ae@sentry-2.alfama.wardenprotocol.org:26656,24ad598e2f3fc82630554d98418d26cc3edf28b9@sentry-3.alfama.wardenprotocol.org:26656
sed -i -e 's/^persistent_peers *=.*/persistent_peers = "'"$PEERS"'"/' $HOME/.warden/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.warden/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.warden/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.warden/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.warden/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.warden/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.0025uward\"|" $HOME/.warden/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/wardend.service > /dev/null <<EOF
[Unit]
Description=Warden Node
After=network-online.target

[Service]
User=$USER
ExecStart=$(which wardend) start --home $HOME/.warden
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
sudo systemctl enable wardend
sudo systemctl restart wardend && sudo journalctl -u wardend -f -o cat
```
