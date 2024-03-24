### How To Install Full Node Lambda Mainnet

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
echo "export LAMBDA_CHAIN_ID=lambda_92000-1" >> $HOME/.bash_profile
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
git clone https://github.com/LambdaIM/lambdavm.git
cd lambdavm
git checkout v1.0.1
make install
```

## Config app
```
lambdavm config chain-id $LAMBDA_CHAIN_ID
```

## Init app
```
lambdavm init $NODENAME --chain-id $LAMBDA_CHAIN_ID
```

### Download configuration
```
wget https://raw.githubusercontent.com/LambdaIM/mainnet/main/lambda_92000-1/genesis.json 
mv genesis.json ~/.lambdavm/config/
curl https://raw.githubusercontent.com/konsortech/Node/main/Mainnet/Lambda/addrbook.json > $HOME/.lambdavm/config/addrbook.json
```

## Set seeds and peers
```
peers="2c4f8e193fd10ab3a2bc919b484fd1c78eceffb3@13.213.214.88:26656,b772a0a8a8ee52c12ff0995ebb670a17db930489@54.225.36.85:26656,277b04415ee88113c304cc3970c88542d6d8f5d3@51.79.91.32:26656,a4ad9857ac5efdd75ec94875b19dd2f0bf562bde@47.75.111.113:26656,13e0e58efbb50df4dc5d39263bda1e432fe204f7@13.229.162.168:26656,ed4fd7dafd7df21f7152d38ee729ec33f505793d@54.254.224.222:26656,53e1c5f1783e839b1b1b51ae57ed2f05b9cdb4f3@13.229.27.15:26656,829503936e022119ce5e9cebf23c8e3a694c70f7@34.159.41.156:26656,d475be798a3b8d9eceb56b5cb276ff75d515cb7b@38.242.215.240:26656,d5bc2c509d730b5211f1e2f4cc95ffbbb6eb6944@194.163.164.52:26656,975afec2ce27ef21eea9d512f68eac8487680b09@135.181.72.187:12123,5a7e747884d496aec70495a767431410edb02167@149.102.139.69:26656,7f07d54901170270d7e7568481867535a363a1d5@65.108.129.104:26656,b029580f30c612176c81df200cf724836bba93c5@49.235.92.21:26656,b2cfe9fa02d93f3fa27cdb45272b5dcf3a075985@138.201.141.76:04656,bdeb4b00fe23900b323a3040a30b81e3c8f82803@23.88.69.167:26989"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" ~/.lambdavm/config/config.toml
```

## Disable indexing
```
indexer="null" && sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.lambdavm/config/config.toml
```

## Config pruning
```
pruning="custom" && \
pruning_keep_recent=100 && \
pruning_keep_every=0 && \
pruning_interval=10 && \
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.lambdavm/config/app.toml && \
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.lambdavm/config/app.toml && \
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.lambdavm/config/app.toml && \
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.lambdavm/config/app.toml
```

## Set minimum gas price and timeout commit
```
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.01ulamb\"/;" $HOME/.lambdavm/config/app.toml
sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"2s\"/" $HOME/.lambdavm/config/config.toml
```

## Create service
```
sudo tee /etc/systemd/system/lambdavm.service > /dev/null <<EOF
[Unit]
Description=lambdavm
After=network-online.target
[Service]
User=$USER
ExecStart=$(which lambdavm) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

## Register and start service
```
sudo systemctl daemon-reload && sudo systemctl enable lambdavm && sudo systemctl restart lambdavm && sudo journalctl -u lambdavm -f -o cat
```
