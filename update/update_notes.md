## 16.11.2022 & upd. 21.11.2022
### Abbreviations:
- **NM** - Nym Mixnode
- **GW** - Gateway
- **NR** - Nym Network Requestor
- **NC** - Nym Client
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

### Install Rust
```
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y
source $HOME/.cargo/env
```

### Download nym binaries
```
cd $HOME
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
git reset --hard
git pull
git checkout nym-binaries-1.1.0
```

## NYM MYXNODE v1.1.0 UPDATING
### Build NM
```
cd $HOME/nym
cargo build -p nym-mixnode --release
sudo mv target/release/nym-mixnode /usr/local/bin/
```
### After installation:
#### Open config file and check if new version is correct, must be `version = '1.1.0'`
`nano ~/.nym/mixnodes/NAME_OF_YOUR_NM/config/config.toml`. 
```
[mixnode]
# Version of the NM for which this configuration was created.
version = '1.1.0'
```
#### If not there are 2 options: 
#### 1. In a config `replace` version manually to correct one
`nano ~/.nym/mixnodes/NAME_OF_YOUR_NM/config/config.toml`    

#### 2. Or run `init` command. This will make these changes too. 
> If nym-mixnode was installed by Nodes.Guru guide:
```
nym-mixnode init --id $node_id --host $(curl ifconfig.me) --wallet-address $wallet
```
> The template of a command, *replace* with **your** `node id` (name of your NM), and `wallet address`
```
nym-mixnode init --id <mixnode_name> --host $(curl ifconfig.me) --wallet-address <wallet-address>
```

#### Be sure you have these API url in config file
`nano ~/.nym/mixnodes/NAME_OF_YOUR_NM/config/config.toml`    
```
## Addresses to APIs running on validator from which the node gets the view of the network.
validator_api_urls = [
        'https://validator.nymtech.net/api',    
]
```
#### If there is empty string add it manually or run command
```
sed -i "s/validator_api_urls = \[/validator_api_urls = \['https:\/\/validator.nymtech.net\/api',/" $HOME/.nym/mixnodes/$node_id/config/config.toml
```

### UPDATING TO v1.1.0 NYM CLIENT, NYM NETWORK REQUESTOR, GATEWAY

#### NC    
- pause your client    
- download new binaries and replace    
- restart the processes    

#### NR
- pause your network requester
- download new binaries and replace    
- restart the processes    

#### GW
- pause your gateway  
- download new binaries and replace    
- restart the process    

### Building NC, GW, NR
- **For Network Requestor install or update go to the [Latest updates part](https://github.com/toolfun/nym-NM-GW-NC-NR-S5/edit/main/update/update_notes.md#latest-updates-network-requestor-21112022)**

- **Client**
```
cd $HOME/nym
git pull
git checkout nym-binaries-1.1.0
cargo build -p nym-client --release
```
- **Gateway**
```
cargo build -p nym-gateway --release
```
```
sudo mv target/release/nym-client /usr/local/bin/
sudo mv target/release/nym-gateway /usr/local/bin/
```
> #### No initialization required

### Be sure the gateway config file contains `nymd urls`. Open config
`~/.nym/gateways/NAME_OF_YOUR_GW/config/config.toml`    
#### and check
```
validator_nymd_urls = [

        'https://rpc.nyx.nodes.guru/',

]
```
#### Change version in a config files of the GW and NC. Config files locations
`~/.nym/gateways/NAME_OF_YOUR_GW/config/config.toml`    
`~/.nym/clients/NAME_OF_YOUR_NC/config/config.toml`

### `!` Rebond the Gateway to a new version v1.1.0 in a NYM wallet (Unbond - Stop GW - Start GW - Bond)
____

> #### `!` So init was on a mixnode only and it was not necessary. Version can be changed manually in a config file.
____

## LATEST UPDATES (Network Requestor 21.11.2022)
```
cd $HOME
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
git reset --hard
git pull
git checkout tags/nym-binaries-1.1.0-network-requester
source $HOME/.bash_profile
cargo build -p nym-network-requester
```
```
sudo mv target/release/nym-network-requester /usr/local/bin/
```
### There is option to download Nym network requestor binarie and replace it
```bash
cd $HOME
wget https://github.com/nymtech/nym/releases/download/nym-binaries-1.1.0-network-requester/nym-network-requester

# Replace binarie
mv nym-network-requester /usr/local/bin/
```

> #### No initialization required. But if the any of the processes are failing, then you might have to run init NR again: this will not overwrite keys or configs, so don't worry

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
