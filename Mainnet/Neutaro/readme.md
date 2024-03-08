### How To Install Full Node Neutaro Mainnet

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
echo "export NEUTARO_CHAIN_ID=Neutaro-1" >> $HOME/.bash_profile
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
  ver="1.20.4"
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
git clone https://github.com/Neutaro/Neutaro
cd Neutaro/cmd/Neutaro/
go build
mv Neutaro /root/go/bin/
```

## Config app
```
Neutaro config chain-id $NEUTARO_CHAIN_ID
```

## Init app
```
Neutaro init $NODENAME --chain-id $NEUTARO_CHAIN_ID
Neutaro config chain-id $NEUTARO_CHAIN_ID
```

### Download configuration
```
cd $HOME
curl -s https://snap1.konsortech.xyz/neutaro/genesis.json > $HOME/.Neutaro/config/genesis.json
curl -s https://snap1.konsortech.xyz/neutaro/addrbook.json > $HOME/.Neutaro/config/addrbook.json
```

## Set seeds and peers
```
seeds="0e24a596dc34e7063ec2938baf05d09b374709e6@109.199.106.233:26656,f0184957ed192e1cdbdaaa69126ea85e4f851d28@mainnet-seed.konsortech.xyz:14165"
peers="ca241438087e75bffcf5bd6739da0c5fc6cdaf60@mainnet-neutaro.konsortech.xyz:14656"
sed -i -e 's|^seeds *=.*|seeds = "'$seeds'"|; s|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.Neutaro/config/config.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.Neutaro/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.Neutaro/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.Neutaro/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.Neutaro/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.Neutaro/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0uneutaro\"|" $HOME/.Neutaro/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/Neutaro.service > /dev/null <<EOF
[Unit]
Description=Neutaro Node
After=network-online.target

[Service]
User=$USER
ExecStart=$(which Neutaro) start --home $HOME/.Neutaro
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
sudo systemctl enable Neutaro
sudo systemctl restart Neutaro && sudo journalctl -u Neutaro -f -o cat
```
