# Preparation - impact protocol 
## Wallet 
`bikin wallet di polkadot.js dua biji controller dan stash catat phrase nya dan simpen baek baek`
## install dependencies dan paste / line
```
sudo apt update && sudo apt upgrade -y
sudo apt install --assume-yes git clang curl libssl-dev llvm libudev-dev make protobuf-compiler
sudo apt install build-essential
```

# install rustup dan pilih nomor 1
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
```
source $HOME/.cargo/env
```
```
rustc --version
rustup default stable
rustup update
rustup update nightly
rustup target add wasm32-unknown-unknown --toolchain nightly
rustup showrustup +nightly show
```
# clone repo and build 
```
git clone https://github.com/GlobalBoost/impactprotocol
cd impactprotocol
cargo build --release
```
# import seed phrase yang dibikin di polkadot.js dan catat public ke nya
```
./target/release/impact import-mining-key "<your 12  mnemonic>" \--base-path /tmp/impactnode \--chain=impact-testnet
```
# jalankan di dalam screen 
```
screen -S impactprotocol 
```
# paste script berikut jangan lupa ganti public key dan name 
```
./target/release/impact \
--base-path /tmp/impactnode \
--chain=impact-testnet \
--port 30333 \
--ws-port 9945 \
--rpc-port 9933 \
--telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
--validator \
--author <your pub key > \
--rpc-methods Unsafe \
--name <your node name> \
--password-interactive
```
# minimize screen 
```
ctrl + a + d
```
# faucet 
`Minta faucet di sini grup signal https://signal.group/#CjQKICa9F2r95FQoGhYjc02lwNgKZCOfDEZngfoWgr_ZkHc4EhAOywghKv4DebEkPsicSCFb`

# lakukan staking di portal 
`https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fimpactnode04.impactprotocol.network%3A9944#/accounts`
```
pergi ke network > stacking 
di dalam tab stacking cari tab `account` dan tambahkan `+validator` 
setup stash ke wallet stash 
setup controller ke wallet controller 
klik bond, 
dan kelar 
```

# change session key / rotating key  dan paste rotating key ke change session key 
```
curl -H "Content-Type: application/json" -d'{"id":1, "jsonrpc":"2.0", "method": "author_rotateKeys", "params":[ ]}' http://localhost:9933
```
