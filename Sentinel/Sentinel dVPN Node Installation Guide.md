# Install Go and Cosmovisor

Feel free to skip this step if you already have Go and Cosmovisor.

## Install Go

We will use Go v1.22.4 as example here. The code below also cleanly removes any previous Go installation.

> sudo rm -rvf /usr/local/go/  
> wget https://golang.org/dl/go1.22.4.linux-amd64.tar.gz  
> sudo tar -C /usr/local -xzf go1.22.4.linux-amd64.tar.gz  
> rm go1.22.4.linux-amd64.tar.gz  

## Configure Go

Unless you want to configure in a non-standard way, then set these in the __~/.profile__ file.

> export GOROOT=/usr/local/go
> export GOPATH=$HOME/go
> export GO111MODULE=on
> export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin

## Install Cosmovisor

We will use Cosmovisor __v1.0.0__ as example here.

> go install github.com/cosmos/cosmos-sdk/cosmovisor/cmd/cosmovisor@v1.0.0

# Install Node

Install the current version of node binary.

> git clone https://github.com/sentinel-official/hub.git sentinel
> cd sentinel
> git checkout v0.11.5
> make install

# Configure Node

## Initialize Node

Please replace __YOUR_MONIKER__ with your own moniker.

> sentinelhub init YOUR_MONIKER --chain-id sentinelhub-2

## Download Genesis

The genesis file link below is Polkachu's mirror download. The best practice is to find the official genesis download link.

> wget -O genesis.json https://snapshots.polkachu.com/genesis/sentinel/genesis.json --inet4-only
> mv genesis.json ~/.sentinelhub/config

## Configure Seed

Using a seed node to bootstrap is the best practice in our view. Alternatively, you can use [addrbook](https://polkachu.com/addrbooks/sentinel) or [persistent_peers](https://polkachu.com/live_peers/sentinel).

> sed -i 's/bootstrap-peers = ""/bootstrap-peers = "ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@seeds.polkachu.com:23956"/' ~/.sentinelhub/config/config.toml

# Launch Node

## Configure Cosmovisor Folder

Create Cosmovisor folders and load the node binary.

### Create Cosmovisor Folders
> mkdir -p ~/.sentinelhub/cosmovisor/genesis/bin
> mkdir -p ~/.sentinelhub/cosmovisor/upgrades

### Load Node Binary into Cosmovisor Folder
> cp ~/go/bin/sentinelhub ~/.sentinelhub/cosmovisor/genesis/bin

## Create Service File

Create a __sentinel.service__ file in the __/etc/systemd/system__ folder with the following code snippet. Make sure to replace USER with your Linux user name. You need sudo privilege to do this step.

> [Unit]
> Description="sentinel node"
> After=network-online.target
>
> [Service]
> User=USER
> ExecStart=/home/USER/go/bin/cosmovisor start
> Restart=always
> RestartSec=3
> LimitNOFILE=4096
> Environment="DAEMON_NAME=sentinelhub"
> Environment="DAEMON_HOME=/home/USER/.sentinelhub"
> Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
> Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
> Environment="UNSAFE_SKIP_BACKUP=true"
> 
> [Install]
> WantedBy=multi-user.target

## Download Snapshot

Please use our popular [snapshot](https://polkachu.com/tendermint_snapshots/sentinel) download service to download and extract Sentinel dVPN snapshot.

## Start Node Service

### Enable service
> sudo systemctl enable sentinel.service

### Start service
> sudo service sentinel start

### Check logs
> sudo journalctl -fu sentinel

# Other Considerations

This installation guide is the bare minimum to get a node started. You should consider the following as you become a more experienced node operator.

1. Use [Ansible script](https://github.com/polkachu/cosmos-validators) to automate the node installation process  
2. Configure firewall to close most ports while only leaving the p2p port (typically 26656) open  
3. Use custom ports for each node so you can run multiple nodes on the same server  

@Polkachu
