# Install Go and Cosmovisor

Feel free to skip this step if you already have Go and Cosmovisor.

## Install Go

We will use Go v1.22.4 as example here. The code below also cleanly removes any previous Go installation.

> sudo rm -rvf /usr/local/go/
> wget https://golang.org/dl/go1.22.4.linux-amd64.tar.gz
> sudo tar -C /usr/local -xzf go1.22.4.linux-amd64.tar.gz
> rm go1.22.4.linux-amd64.tar.gz
