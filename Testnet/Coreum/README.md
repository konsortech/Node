### How To Install Full Node Coreum Testnet

## Setting up vars
```
export CORE_CHAIN_ID="coreum-testnet-1"
export CORE_DENOM="utestcore"
export CORE_NODE="https://full-node-pluto.testnet-1.coreum.dev"
export CORE_FAUCET_URL="https://api.testnet-1.coreum.dev"
export CORE_COSMOVISOR_VERSION="v1.3.0"
export CORE_VERSION="v0.1.1"

export CORE_CHAIN_ID_ARGS="--chain-id=$CORE_CHAIN_ID"
export CORE_NODE_ARGS="--node=$CORE_NODE $CORE_CHAIN_ID_ARGS"

export CORE_HOME=$HOME/.core/"$CORE_CHAIN_ID"

export CORE_BINARY_NAME=$(arch | sed s/aarch64/cored-linux-arm64/ | sed s/x86_64/cored-linux-amd64/)
export COSMOVISOR_TAR_NAME=cosmovisor-$CORE_COSMOVISOR_VERSION-linux-$(arch | sed s/aarch64/arm64/ | sed s/x86_64/amd64/).tar.gz

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

# Download and build binaries
### Create a proper folder structure for cored.
```
mkdir -p $CORE_HOME/bin
```
### Download cored and put it in the required folder.
```
curl -LO https://github.com/CoreumFoundation/coreum/releases/download/$CORE_VERSION/$CORE_BINARY_NAME
mv $CORE_BINARY_NAME $CORE_HOME/bin/cored
```
### Add cored to PATH and make it executable.

```
chmod +x $CORE_HOME/bin/*
```
### Test cored
```
cored version
```

### Set the moniker variable to reuse it in the following instructions.
```
export MONIKER="full"
```


## Config app

### Set Node config
```
CORE_NODE_CONFIG=$CORE_HOME/config/config.toml
```
### Your Node should have public IP, which you should set there:
```
CORE_EXTERNAL_IP={YOUR_VM_PUBLIC_IP}
```
### Update node config with CORE_EXTERNAL_IP
```
crudini --set $CORE_NODE_CONFIG p2p addr_book_strict false
crudini --set $CORE_NODE_CONFIG p2p external_address "\"tcp://$CORE_EXTERNAL_IP:26656\""
crudini --set $CORE_NODE_CONFIG rpc laddr "\"tcp://0.0.0.0:26657\""
```


## Init app
```
cored init $MONIKER $CORE_CHAIN_ID_ARGS
```

### Set the config path variables.
```
CORE_APP_CONFIG=$CORE_HOME/config/app.toml
```

## (Optional) Enable REST APIs disabled by default.
```
crudini --set $CORE_APP_CONFIG api enable true # enable API
crudini --set $CORE_APP_CONFIG api swagger true # enable swagger UI for the API
```

## (Optional) Enable prometheus monitoring.Disable indexing
```
crudini --set $CORE_NODE_CONFIG instrumentation prometheus true

```
# Create service
```
sudo tee /etc/systemd/system/cored.service > /dev/null << EOF
[Unit]
Description=Coreum Node
After=network-online.target
[Service]
User=$USER
ExecStart=/root/.core/coreum-testnet-1/bin/cored start
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
sudo systemctl enable cored
sudo systemctl restart cored && sudo journalctl -u cored -f -o cat
```

## After sync 
### create wallet 
```
cored keys add $MONIKER --keyring-backend os 
```
### or recover old wallet 
```
cored keys add $MONIKER --keyring-backend os --recover
```
## Check balance 
```
cored q bank balances  $(cored keys show $MONIKER --address --keyring-backend os) --denom $CORE_DENOM
```
### Wait until node is fully synced(its important). To check sync status run next command:

```
echo "catching_up: $(echo  $(cored status) | jq -r '.SyncInfo.catching_up')"
```
# Create validator
## set up validator configuration And change your variable below
```
 export CORE_VALIDATOR_DELEGATION_AMOUNT=20000000000 
 export CORE_VALIDATOR_NAME="YourValidatorName" 
 export CORE_VALIDATOR_WEB_SITE="YourSite.com" 
 export CORE_VALIDATOR_IDENTITY="Your Keybase" 
 export CORE_VALIDATOR_COMMISSION_RATE="0.10"
 export CORE_VALIDATOR_COMMISSION_MAX_RATE="0.20" 
 export CORE_VALIDATOR_COMMISSION_MAX_CHANGE_RATE="0.01"
 export CORE_MIN_DELEGATION_AMOUNT=20000000000 
```

## create validator

```cored tx staking create-validator \
--amount=$CORE_VALIDATOR_DELEGATION_AMOUNT$CORE_DENOM \
--pubkey="$(cored tendermint show-validator)" \
--moniker="$CORE_VALIDATOR_NAME" \
--website="$CORE_VALIDATOR_WEB_SITE" \
--identity="$CORE_VALIDATOR_IDENTITY" \
--commission-rate="$CORE_VALIDATOR_COMMISSION_RATE" \
--commission-max-rate="$CORE_VALIDATOR_COMMISSION_MAX_RATE" \
--commission-max-change-rate="$CORE_VALIDATOR_COMMISSION_MAX_CHANGE_RATE" \
--min-self-delegation=$CORE_MIN_DELEGATION_AMOUNT \
--gas auto \
--chain-id="$CHAINID" \
--from=$MONIKER \
--keyring-backend os -y -b block $CORE_CHAIN_ID_ARGS```
```
# Check the validator status.
```
cored q staking validator "$(cored keys show $MONIKER --bech val --address $CORE_CHAIN_ID_ARGS)"
```
