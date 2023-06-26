
# [Neutron](https://neutron.org/) Create Validator High Availability with Tendermint KMS

[Terndermint KMS](https://github.com/iqlusioninc/tmkms#about) is Key Management System for Tendermint applications such as Cosmos Validators.
This repository contains tmkms, a key management service intended to be deployed in conjunction with Tendermint applications (ideally on separate physical hosts) which provides the following:

- High-availability access to validator signing keys
- Double-signing prevention even in the event the validator process is compromised
- Hardware security module storage for validator keys which can survive host compromise

So lets start to begin.

# [Run Your Full Node and Create Validator First](https://github.com/konsortech/Node/blob/main/Neutron/Tendermint_KMS/readme.md)

## Prerequisites

We recommended you to run KMS service in a separate machine. Becasue this service work for just in case if your another validators down, you have a backup validator.  </br>

| Hardware |	Chunk-Only Producer Specification |
| -------- | ----------------------------------   |
| OS       | Linux Ubuntu 20.04         |
| CPU Architectures      | X86_64        |

## Install All Dependencies

Rust
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
```

GCC
```
sudo apt update -y
sudo apt install git build-essential ufw curl jq snapd -y
```

Libusb
```
apt install libusb-1.0-0-dev
export RUSTFLAGS=-Ctarget-feature=+aes,+ssse3
```

## Install & Setup TMKMS

```
cd $HOME
git clone https://github.com/iqlusioninc/tmkms.git
cd $HOME/tmkms
cargo install tmkms --features=softsign
tmkms init config
tmkms softsign keygen ./config/secrets/secret_connection_key
```

## Copy your validator keys from Your Node Server

```
rsync -Pavz root@(your_node_ip):~/.neutrond/config/priv_validator_key.json ~/tmkms/config/secrets
```
  Change your_node_ip with your Node Public IP Address.
  
## import validator key into tmkms 

```
tmkms softsign import $HOME/tmkms/config/secrets/priv_validator_key.json $HOME/tmkms/config/secrets/priv_validator_key
```

## Config tmkms to Neutron Testnet Chain ID
```
nano $HOME/tmkms/config/tmkms.toml
```
```
# Tendermint KMS configuration file

## Chain Configuration

### Cosmos Hub Network

[[chain]]
id = "quark-1"
key_format = { type = "cosmos-json", account_key_prefix = "neutronpub", consensus_key_prefix = "neutronvalconspub" }
state_file = "/root/tmkms/config/state/priv_validator_state.json"

## Signing Provider Configuration

### Software-based Signer Configuration

[[providers.softsign]]
chain_ids = ["quark-1"]
key_type = "consensus"
path = "/root/tmkms/config/secrets/priv_validator_key"

## Validator Configuration

[[validator]]
chain_id = "quark-1"
addr = "tcp://(your_node_ip):688" # your validator node ip and port
secret_key = "/root/tmkms/config/secrets/secret_connection_key"
protocol_version = "v0.34"
reconnect = true
```

## Create Service for TMKMS
```
sudo tee /etc/systemd/system/tmkms.service > /dev/null <<EOF
[Unit]
Description=tmkms
After=network-online.target

[Service]
User=$USER
ExecStart=$(which tmkms) start -c $HOME/tmkms/config/tmkms.toml
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

## Access and Modify Your Config Node and Validator

```
nano $HOME/.neutrond/config/config.toml
```

Add into config.toml
```
priv_validator_laddr = "tcp://0.0.0.0:688"
```

Comment or remark this 
```
# Path to the JSON file containing the private key to use as a validator in the consensus protocol
# priv_validator_key_file = "config/priv_validator_key.json"

# Path to the JSON file containing the last sign state of a validator
# priv_validator_state_file = "data/priv_validator_state.json"
```

Restart Neutrond Services
```
systemctl restart neuntrond && sudo journalctl -u neuntrond -f -o cat
```

Go back to your KMS Server and start tmkms service
```
sudo systemctl daemon-reload
sudo systemctl enable tmkms
sudo systemctl restart tmkms && sudo journalctl -u tmkms -f -o cat
```
Congrats, You should now be signing blocks! If you cancel the TMKMS process, you will no longer sign blocks and will stop syncing. If you restart the TMKMS process, your validator node will continue to sync from where it left off.

## How To Check Result

Check on Your Node Validator Server, if that showing  your Address and Pubkey that Successfully
```
neutrond status 2>&1 | jq .ValidatorInfo
```

Check On Tendermint KMS Server, find and search this "state connected to validator successfully" after you start kms services
```
journalctl -u tmkms -f -o cat
```
