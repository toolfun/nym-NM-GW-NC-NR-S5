#### `This is how-to-update-only manual`
> 
> **NM** - Nym node: mixnode mode    
> **GW** - Nym node: gateway mode

# Upd. 09.07.2024 Nym Binaries v2024.7-doubledecker

Releases    
https://github.com/nymtech/nym/releases/tag/nym-binaries-v2024.7-doubledecker    
Changelog    
https://github.com/nymtech/nym/blob/nym-binaries-v2024.7-doubledecker/CHANGELOG.md



<!-- ################################################################
> **NR** - Nym node, mode Network Requestor    
################################################################ -->


### Update and install tools
```
sudo apt update && sudo apt upgrade -y
```
```
sudo dpkg --configure -a
sudo apt install make clang pkg-config libssl-dev build-essential git jq
```

### Install or update Rust
```
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y
source $HOME/.cargo/env
rustup update
```

#

### 🟠 NM UPDATING
### Build nym-node
```bash
cd $HOME
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
git checkout master
cargo build --release --bin nym-node
```

<!--
git checkout release/ v 1_1_15
-->

### Replace and restart
```
sudo systemctl stop nym-node && \
sudo mv ~/nym/target/release/nym-node $(which nym-node) && \
sudo systemctl restart nym-node && sudo journalctl -u nym-node -f -o cat
```

### Change mixnode version to the 1.1.4 in the Nym Wallet (Bonding - Node Settings section)

#

### 🟢 **GW UPDATING**
### Build nym-node
```
cd $HOME
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
git checkout master
cargo build --release --bin nym-node
```

### Replace and restart
```bash
sudo systemctl stop nym-node && \
sudo mv ~/nym/target/release/nym-node $(which nym-node) && \
sudo systemctl restart nym-node && sudo journalctl -u nym-node -f -o cat
```

### Change version to 1.1.4 in the Nym Wallet
Menu Bonding -> Gateway Settings    
> ![](https://github.com/toolfun/_pics/blob/988df446b0c9c368b68d03503a56b8b74362b505/gwsett.jpg)    
> ![](https://github.com/toolfun/_pics/blob/988df446b0c9c368b68d03503a56b8b74362b505/gwsett2.jpg)    

<!-- ######################################### Service #############

```
[Unit]
Description=Nym-node-GW

[Service]
User=$USER
ExecStart=/usr/local/bin/nym-node run --id <NODE_ID> --mode exit-gateway --deny-init --accept-operator-terms-and-conditions
KillSignal=SIGINT
Restart=on-failure
RestartSec=5
StartLimitInterval=350
StartLimitBurst=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
```


```
[Unit]
Description=Nym-node-mixnode

[Service]
User=$USER
ExecStart=/usr/local/bin/nym-node run --id <NODE_ID> --mode mixnode --deny-init --public-ips <IPv4> --accept-operator-terms-and-conditions
KillSignal=SIGINT
Restart=on-failure
RestartSec=10
StartLimitInterval=350
StartLimitBurst=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```


######################################### Service ############# -->

<!-- ---------------------------- node move
  copy old server
nym-nodes dir, service file

update
ulimit
ufw

  change on new server
node ID if needed
node path if the username changed
[example /home/<USERNAME>/.nym/nym-nodes/<NODE-ID>/data/x25519_noise.pub]
IP


----------------------------- node move -->
