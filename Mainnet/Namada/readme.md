### How To Install Full Node Namada Mainnet

## Setting up vars
Save variables to system
```
echo "export ALIAS=Your_Alias_Validator" >> $HOME/.bash_profile
echo "export CHAIN_ID=namada.5f5de2dd1b88cba30586420" >> ~/.bash_profile
echo "export WALLET=wallet" >> ~/.bash_profile
echo "export BASE_DIR="$HOME/.local/share/namada"" >> $HOME/.bash_profile

source $HOME/.bash_profile
# set configs server
export NAMADA_NETWORK_CONFIGS_SERVER="https://github.com/anoma/namada-mainnet-genesis/releases/download/mainnet-genesis"
```

## Update packages
```
sudo apt update && sudo apt upgrade -y
sudo apt-get install -y make git-core libssl-dev pkg-config libclang-12-dev build-essential protobuf-compiler ccze
```

## Install dependencies
```
sudo apt update && sudo apt upgrade -y
sudo apt-get install -y make git-core libssl-dev pkg-config libclang-12-dev build-essential protobuf-compiler
```

## Install Go
```
cd $HOME
! [ -x "$(command -v go)" ] && {
VER="1.20.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
}
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

## Install RUST
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source $HOME/.cargo/env
```

## Install CometBFT
```
cd $HOME
rm -rf cometbft
git clone https://github.com/cometbft/cometbft.git
cd cometbft
git checkout v0.37.15
make build
sudo cp $HOME/cometbft/build/cometbft /usr/local/bin/
cometbft version
```

## Namada Download and build binaries
```
cd $HOME
rm -rf namada
git clone https://github.com/anoma/namada
cd namada
wget https://github.com/anoma/namada/releases/download/v101.1.1/namada-v101.1.1-Linux-x86_64.tar.gz
tar -xvf namada-v101.1.1-Linux-x86_64.tar.gz
rm namada-v101.1.1-Linux-x86_64.tar.gz
cd namada-v101.1.1-Linux-x86_64
sudo mv namada* /usr/local/bin/
if [ ! -d "$BASE_DIR" ]; then
    mkdir -p "$BASE_DIR"
fi
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
namadac utils join-network --chain-id $CHAIN_ID
```

## Set seeds and peers
```
SEEDS="10f2016ec436bee02d0cf3e5499f3c99d5a93d26@mainnet-seed.konsortech.xyz:18265"
PEERS="2dd4a3d6f44f2514041171d0efe7525cd29ade4b@mainnet-namada.konsortech.xyz:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.hid-node/config/config.toml
```

## Create service
```
sudo tee /etc/systemd/system/namadad.service > /dev/null <<EOF
[Unit]
Description=namada
After=network-online.target

[Service]
User=$USER
WorkingDirectory=$BASE_DIR
Environment=CMT_LOG_LEVEL=p2p:none,pex:error
Environment=NAMADA_CMT_STDOUT=true
Environment=NAMADA_BROADCASTER_TIMEOUT_SECS=600
ExecStart=$(which namada) node ledger run
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
sudo systemctl start namadad && journalctl -fu namadad -o cat
```
