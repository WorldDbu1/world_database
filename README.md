World Batabase

## Installation and configuration environment

wget https://golang.org/dl/go1.16.linux-amd64.tar.gz

tar xvf go1.16.linux-amd64.tar.gz  -C  /usr/local

sudo sh -c 'echo "export PATH=$PATH:/usr/local/go/bin" >> /etc/profile'

source /etc/profile

apt update

apt install screen mesa-opencl-icd ocl-icd-opencl-dev gcc git bzr jq pkg-config curl clang build-essential hwloc libhwloc-dev wget -y && sudo apt upgrade -y


curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

source $HOME/.cargo/env


## Set environment variables

export FFI_BUILD_FROM_SOURCE=1 

export RUSTFLAGS="-C target-cpu=native -g"

export FIL_PROOFS_PARAMETER_CACHE=/data/.parameter 

export FIL_PROOFS_PARENT_CACHE=/data/.parent

# if run in china

export IPFS_GATEWAY=https://proof-parameters.s3.cn-south-1.jdcloud-oss.com/ipfs/


## Install and compile

cd /root

git clone https://github.com/WorldDbu1/world_database

git clone https://github.com/WorldDbu1/world_database_change

cd filecoin

cd extern 

git clone https://github.com/filecoin-project/filecoin-ffi

git clone https://github.com/filecoin-project/serialization-vectors

git clone https://github.com/filecoin-project/test-vectors

cd ../

go clean -modcache

go mod tidy  

## Modify the extension library

rm -rf /root/go/pkg/mod/github.com/filecoin-project/go-address@v0.0.5

rm -rf /root/go/pkg/mod/github.com/filecoin-project/specs-actors

rm -rf /root/go/pkg/mod/github.com/filecoin-project/specs-actors@v0.9.14

cp -r /root/filecoin_change/go-address@v0.0.5 /root/go/pkg/mod/github.com/filecoin-project/go-address@v0.0.5

cp -r /root/filecoin_change/specs-actors /root/go/pkg/mod/github.com/filecoin-project/specs-actors

cp -r /root/filecoin_change/specs-actors@v0.9.14 /root/go/pkg/mod/github.com/filecoin-project/specs-actors@v0.9.14

make 2k


## Compilation is complete

./lotus fetch-params 536870912

./lotus-seed pre-seal --sector-size 536870912 --num-sectors 1

./lotus-seed genesis new localnet.json 

./lotus-seed genesis add-miner localnet.json ~/.genesis-sectors/pre-seal-w01000.json


## start up

./lotus daemon --lotus-make-genesis=genesis.car --genesis-template=localnet.json --bootstrap=false


## Import wallet

./lotus wallet import --as-default ~/.genesis-sectors/pre-seal-w01000.key


## Configure founding miner miner operation

./lotus-miner init --genesis-miner --actor=w01000 --pre-sealed-sectors=/root/.genesis-sectors --pre-sealed-metadata=/root/.genesis-sectors/pre-seal-w01000.json --nosync


## Run miner

./lotus-miner run --nosync


## View address

./lotus net listen

## Join the master node

./lotus net connect /ip4/***.***.***.***/tcp/****/p2p/****
