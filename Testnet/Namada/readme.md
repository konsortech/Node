### How To Install Full Node Namada Testnet

## Setting up vars
Save variables to system
```
echo "export ALIAS=Your_Alias_Validator" >> $HOME/.bash_profile
echo "export NAMADA_TAG=v0.13.3" >> ~/.bash_profile
echo "export TM_HASH=v0.1.4-abciplus" >> ~/.bash_profile
echo "export CHAIN_ID=public-testnet-15.0dacadb8d663" >> ~/.bash_profile
echo "export WALLET=wallet" >> ~/.bash_profile

source $HOME/.bash_profile
```

## Update packages
```
sudo apt update && sudo apt upgrade -y
sudo apt-get install -y make git-core libssl-dev pkg-config libclang-12-dev build-essential protobuf-compiler ccze
```

## Install dependencies
```
sudo apt install curl tar wget clang pkg-config libssl-dev libclang-dev -y
sudo apt install jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
sudo apt install -y uidmap dbus-user-session
```

## Install Protocol Buffers
```
cd $HOME
curl -L -o protobuf.zip https://github.com/protocolbuffers/protobuf/releases/download/v24.4/protoc-24.4-linux-x86_64.zip
mkdir protobuf_temp && unzip protobuf.zip -d protobuf_temp/
sudo cp protobuf_temp/bin/protoc /usr/local/bin/
sudo cp -r protobuf_temp/include/* /usr/local/include/
rm -rf protobuf_temp protobuf.zip
```

## Install CometBFT
```
cd $HOME
curl -L -o protobuf.zip https://github.com/protocolbuffers/protobuf/releases/download/v24.4/protoc-24.4-linux-x86_64.zip
mkdir protobuf_temp && unzip protobuf.zip -d protobuf_temp/
sudo cp protobuf_temp/bin/protoc /usr/local/bin/
sudo cp -r protobuf_temp/include/* /usr/local/include/
rm -rf protobuf_temp protobuf.zip
```

## Namada Download and build binaries
```
cd $HOME
rm -rf $HOME/namada
git clone https://github.com/anoma/namada
cd $HOME/namada
wget https://github.com/anoma/namada/releases/download/v0.28.1/namada-v0.28.1-Linux-x86_64.tar.gz
tar -xvf namada-v0.28.1-Linux-x86_64.tar.gz
rm namada-v0.28.1-Linux-x86_64.tar.gz
cd namada-v0.28.1-Linux-x86_64
sudo mv namada namadan namadac namadaw /usr/local/bin/
```

## Check namada Version
```
namada --version
```

## Init app
For Pre-Genesis
```
cd $HOME
cp -r ~/.namada/pre-genesis $BASE_DIR/
namada client utils join-network --chain-id $CHAIN_ID --genesis-validator $ALIAS
```

For Post-Genesis
```
namada client utils join-network --chain-id $CHAIN_ID
```

### Download configuration
```
cd $HOME && wget "https://github.com/heliaxdev/anoma-network-config/releases/download/public-testnet-3.0.81edd4d6eb6/public-testnet-3.0.81edd4d6eb6.tar.gz"
tar xvzf "$HOME/public-testnet-3.0.81edd4d6eb6.tar.gz"
```

## Set seeds and peers
```
SEEDS="ab7479dc1dd3cbd2b6a0d627a3e7e1eb7141594b@testnet-seed.konsortech.xyz:12165"
PEERS=""
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.hid-node/config/config.toml
```

## Set minimum gas price
```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0uhid\"/" $HOME/.hid-node/config/app.toml
```

## Create service
```
sudo tee /etc/systemd/system/namadad.service > /dev/null <<EOF
[Unit]
Description=namada
After=network-online.target
[Service]
User=root
WorkingDirectory=$HOME/.namada
Environment=NAMADA_LOG=debug
Environment=NAMADA_TM_STDOUT=true
ExecStart=/usr/local/bin/namada --base-dir=$HOME/.namada node ledger run 
StandardOutput=syslog
StandardError=syslog
Restart=always
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

## Register and start service
```
sudo systemctl daemon-reload
sudo systemctl enable namadad
sudo systemctl start namadad && journalctl -fu namadad -o cat | ccze
```
