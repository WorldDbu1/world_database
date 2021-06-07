FILECOIN


## 安装配置环境

wget https://golang.org/dl/go1.16.linux-amd64.tar.gz

tar xvf go1.16.linux-amd64.tar.gz  -C  /usr/local

sudo sh -c 'echo "export PATH=$PATH:/usr/local/go/bin" >> /etc/profile'

source /etc/profile

apt update

apt install screen mesa-opencl-icd ocl-icd-opencl-dev gcc git bzr jq pkg-config curl clang build-essential hwloc libhwloc-dev wget -y && sudo apt upgrade -y


curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

source $HOME/.cargo/env


## 设置环境变量

export FFI_BUILD_FROM_SOURCE=1 

export RUSTFLAGS="-C target-cpu=native -g"

export FIL_PROOFS_PARAMETER_CACHE=/data/.parameter 

export FIL_PROOFS_PARENT_CACHE=/data/.parent

如果是国内则设置

export IPFS_GATEWAY=https://proof-parameters.s3.cn-south-1.jdcloud-oss.com/ipfs/


## 安装编译

git clone https://gitee.com/wsas845/filecoin

git clone https://gitee.com/wsas845/filecoin_change

cd filecoin

cd extern 

git clone https://github.com/filecoin-project/filecoin-ffi

git clone https://github.com/filecoin-project/serialization-vectors

git clone https://github.com/filecoin-project/test-vectors

cd ../

go clean -modcache

go mod tidy  

根据 https://gitee.com/wsas845/filecoin_change 修改扩展库

rm -rf /root/go/pkg/mod/github.com/filecoin-project/go-address@v0.0.5

rm -rf /root/go/pkg/mod/github.com/filecoin-project/specs-actors

rm -rf /root/go/pkg/mod/github.com/filecoin-project/specs-actors@v0.9.14

cp -r /root/filecoin_change/go-address@v0.0.5 /root/go/pkg/mod/github.com/filecoin-project/go-address@v0.0.5

cp -r /root/filecoin_change/specs-actors /root/go/pkg/mod/github.com/filecoin-project/specs-actors

cp -r /root/filecoin_change/specs-actors@v0.9.14 /root/go/pkg/mod/github.com/filecoin-project/specs-actors@v0.9.14

make 2k


## 编译后

./lotus fetch-params 536870912

./lotus-seed pre-seal --sector-size 536870912 --num-sectors 1

./lotus-seed genesis new localnet.json 

./lotus-seed genesis add-miner localnet.json ~/.genesis-sectors/pre-seal-w01000.json


## 启动

./lotus daemon --lotus-make-genesis=genesis.car --genesis-template=localnet.json --bootstrap=false


## 导入钱包

./lotus wallet import --as-default ~/.genesis-sectors/pre-seal-w01000.key


## 配置创始矿工 矿工运行

./lotus-miner init --genesis-miner --actor=t01000 --pre-sealed-sectors=~/.genesis-sectors --pre-sealed-metadata=~/.genesis-sectors/pre-seal-w01000.json --nosync


## 运行矿工

./lotus-miner run --nosync


## 查看地址

./lotus net listen

## 加入主节点

./lotus net connect /ip4/***.***.***.***/tcp/****/p2p/****
