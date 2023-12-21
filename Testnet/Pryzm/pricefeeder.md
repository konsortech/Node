
### How To Install & Run Pryzm Pricefeeder

## Install Docker & Docker Compose

Followed this Link

>[Install Docker](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04)

>[Install Docker Compose](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-22-04)

## Download Your Pricefeeder resource

```
cd $HOME
mkdir -p $HOME/pricefeeder $$ cd pricefeeder

wget https://storage.googleapis.com/pryzm-zone/feeder/config.yaml
wget https://storage.googleapis.com/pryzm-zone/feeder/init.sql
wget https://storage.googleapis.com/pryzm-zone/feeder/docker-compose.yml
```

## Download docker image
```
docker pull europe-docker.pkg.dev/pryzm-zone/core/pryzm-feeder:0.3.4
docker pull timescale/timescaledb:2.13.0-pg16
```
## Create New Wallet For Przym Pricefeeder
Save your price feeder wallet address and save mnemonic
```
pryzmd keys add pricefeeder
```

## Edit your config.yml
```
nano config.yml
```
```
feeder: "pryzmxxxxxxx" # Filled with your pryzm pricefeeder wallet address (you may use validator address, but we choose create new one)
feederMnemonic: "" # Filled with your mnemonic pricefeeder wallet address
validator: "przymvaloperxxxxx" # Filled with your validator address
```
## Check Your Folder path is correctly
Make sure you have correct this folder and file path
```
ls $HOME/pricefeeder
# config.yaml  docker-compose.yml  init.sql
```

## Add Balances
fund your pricefeeder wallet address with faucet
> [Pryzm Faucet](https://testnet.pryzm.zone/faucet)

## Run your Pricefeeder
```
docker compose up -d
```

## Check your logs
```
docker logs -f pryzm-feeder
```
```
[2023-12-21T10:32:28.166Z] databaseService info: service started
[2023-12-21T10:32:28.172Z] assetsHostChainMonitoringService info: service started
[2023-12-21T10:32:28.173Z] osmoMonitoringService info: service started
[2023-12-21T10:32:28.174Z] pryzmMonitoringService info: service started
[2023-12-21T10:32:28.175Z] icStakingHostChainMonitoringService info: service started
[2023-12-21T10:32:28.176Z] assetsPlugin info: plugin started
[2023-12-21T10:32:28.176Z] ammPlugin info: plugin started
[2023-12-21T10:32:28.176Z] icStakingPlugin info: plugin started
[2023-12-21T10:32:28.188Z] TelemetryServer info: ⚡️ express server is running at port: 2121
```
