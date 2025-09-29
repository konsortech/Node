### How To Install Full Node Cosmoshub Mainnet

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
echo "export COSMOS_CHAIN_ID=cosmoshub-4" >> $HOME/.bash_profile
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
  ver="1.23.5"
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
rm -rf cosmos
git clone https://github.com/cosmos/gaia cosmos
cd cosmos
git checkout v25.1.0
make install
```

## Config app
```
gaiad config chain-id $COSMOS_CHAIN_ID
```

## Init app
```
gaiad init $NODENAME --chain-id $COSMOS_CHAIN_ID
```

### Download configuration
```
curl -o ~/.gaia/config/addrbook.toml https://snapshot.konsortech.xyz/mainnet/cosmos/addrbook.json
curl -o ~/.gaia/config/genesis.json https://snapshot.konsortech.xyz/mainnet/cosmos/genesis.json
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.gaia/config/config.toml
```

## Set minimum gas price
```
sed -i 's|^minimum-gas-prices *=.*|minimum-gas-prices = "0aislm"|g' $HOME/.gaia/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/gaiad.service > /dev/null << EOF
[Unit]
Description=Cosmoshub Node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which gaiad) start
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
sudo systemctl enable gaiad
sudo systemctl restart gaiad && sudo journalctl -u gaiad -f -o cat
```



