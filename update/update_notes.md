# Upd. 30.11.2022. New binaries 1.1.1
### Abbreviations:
- **NM** - Nym Mixnode
- **GW** - Gateway
- **NR** - Nym Network Requestor
- **NC** - Nym Client
- **S5** - Nym Socks5 Client    


### Nym binaries page
https://github.com/nymtech/nym/releases/tag/nym-binaries-v1.1.1
____

### Update and install tools
```
sudo apt update && sudo apt upgrade -y
```
```
sudo dpkg --configure -a
```
```
sudo apt install curl wget ufw make clang pkg-config libssl-dev build-essential git htop
```

### Install or update Rust
```
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y
source $HOME/.cargo/env
```

### ⏬ First, clone Nym repository. And then we will build each release
```
cd $HOME
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
git reset --hard
git pull
git checkout nym-binaries-v1.1.1
```

### 🟠 NM UPDATING
### Build NM
```
cd $HOME/nym
cargo build -p nym-mixnode --release
```
```
sudo systemctl stop nym-mixnode
sudo mv target/release/nym-mixnode /usr/local/bin/
```
```
sudo systemctl daemon-reload
sudo systemctl enable nym-mixnode
sudo systemctl restart nym-mixnode
```
### After installation:
#### Open config and check if new version is correct, must be `version = '1.1.1'`
`nano ~/.nym/mixnodes/NAME_OF_YOUR_NM/config/config.toml`. 
```
[mixnode]
# Version of the NM for which this configuration was created.
version = '1.1.1'
```
#### If not there are 2 options: 
#### 1. In config `replace` version manually to correct one
`nano ~/.nym/mixnodes/YOUR_MIXNODE_NAME/config/config.toml`    

#### 2. Or run `init` command. This will make these changes too. 
> *Replace* <YOUR_MIXNODE_NAME> with **your** `node id` (name of your Nym mixnode), and <WALLET_ADDRESS> with your `wallet address`
```
nym-mixnode init --id <YOUR_MIXNODE_NAME> --host $(curl ifconfig.me) --wallet-address <WALLET_ADDRESS>
```

#### Be sure you have these `API url` in config file
`nano ~/.nym/mixnodes/YOUR_MIXNODE_NAME/config/config.toml`    
```
## Addresses to APIs running on validator from which the node gets the view of the network.
validator_api_urls = [
        'https://validator.nymtech.net/api',    
]
```
#### If there is empty string add it manually as it shown above

### UPDATING NC, NR, GW

### 🟣 **NC**    
```
cd $HOME/nym
cargo build -p nym-client --release
```
#### Change version in config of the NC
`nano ~/.nym/clients/NAME_OF_YOUR_NC/config/config.toml`
```
sudo systemctl stop nym-client
sudo mv target/release/nym-client /usr/local/bin/
```
```
sudo systemctl daemon-reload
sudo systemctl enable nym-client
sudo systemctl restart nym-client
```

### 🔵 **NR**    
```
cd $HOME/nym
cargo build -p nym-network-requester
```
> No initialization required. But if the any of the processes are failing, then you might have to run init NR again: this will not overwrite keys or configs, so don't worry
```
sudo systemctl stop nym-network-requester
sudo mv target/release/nym-network-requester /usr/local/bin/
```
```
sudo systemctl daemon-reload
sudo systemctl enable nym-network-requester
sudo systemctl restart nym-network-requester
```

### 🟢 **GW**    
```
cd $HOME/nym
cargo build -p nym-gateway --release
```
```
sudo systemctl stop nym-gateway
sudo mv target/release/nym-gateway /usr/local/bin/
```
### Be sure the gateway config file contains `nymd urls`. Open config
`~/.nym/gateways/NAME_OF_YOUR_GW/config/config.toml`    
#### and check
```
validator_nymd_urls = [

        'https://rpc.nyx.nodes.guru/',

]
```
#### Change version in config of the GW
`nano ~/.nym/gateways/NAME_OF_YOUR_GW/config/config.toml`    
### ~~Rebond the Gateway to a new version v1.1.1 in a NYM wallet (Unbond - Stop GW - Start GW - Bond)~~
____

> #### `!` So **init** was on a mixnode only and it was not necessary. Version can be changed manually in a config file.
____

### ⚫ Socks5 external(❗) client
##### ⚠ Be careful to protect the entrypoint properly as anyone who can reach it will be able to use it, eating your traffic
##### Check GW via external Nym socks5 client
```bash
# .
# Install Nym socks5 client on external server, and edit configuration to be able connect:
# Git clone repo
git clone https://github.com/nymtech/nym.git    

# Change 127.0.0.1 to 0.0.0.0.  *
nano ~/nym/clients/socks5/src/socks/server.rs    

# Then build socks5 client
cd nym
git reset --hard
git pull
git checkout nym-binaries-v1.1.1
cargo build -p nym-socks5-client --release
sudo mv target/release/nym-socks5-client /usr/local/bin/    

# Init socks5 client
nym-socks5-client init --id <socks5 client name> --provider <your service provider>
# For example:
# nym-socks5-client init --id my_socks5 --provider GegdtpNzYj4QCgpih9Kxv7ZVZxmVdxYHsDkiPsbT71XG.E8xtE8mrapjzFtyuziZSrsScAKhwZMH5wNpKWtKfzJ5Y@9Byd9VAtyYMnbVAcqdoQxJnq76XEg2dbxbiF5Aa5Jj9J --gateway 9Byd9VAtyYMnbVAcqdoQxJnq76XEg2dbxbiF5Aa5Jj9J    

# Start s5 client
./nym-socks5-client run --id <socks5 client name>

# !!! Of course, be careful to protect the entrypoint properly as anyone who can reach it will be able to use it, eating your traffic!
```
____

### Just...

### Service file for NR
```
[Unit]
Description=Nym Network Requester

[Service]
User=root
ExecStart=/usr/local/bin/nym-network-requester run --enable-statistics
KillSignal=SIGINT
Restart=on-failure
RestartSec=30
StartLimitInterval=350
StartLimitBurst=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
```
