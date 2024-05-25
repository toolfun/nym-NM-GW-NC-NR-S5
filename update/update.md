# 🚧🚧🚧 under construction

#### `This is how-to-update manual`

# Upd. 24.05.2024 v2024.5-ragusa

> **NM** - Nym node, mode Mixnode
> **GW** - Nym node, mode Gateway

<!-- ################################################################
> **NR** - Nym node, mode Network Requestor    
################################################################ -->

#### Nym binaries
https://github.com/nymtech/nym/releases/tag/nym-binaries-v2024.5-ragusa

____


### Update and install tools
```
sudo apt update && sudo apt upgrade -y
```
```
sudo dpkg --configure -a
sudo apt install curl wget ufw make clang pkg-config libssl-dev build-essential git jq htop
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
git checkout nym-binaries-v2024.5-ragusa
cargo build --release --bin nym-node
```

<!--
git checkout release/ v 1_1_15
-->

### Replace and restart
```
sudo systemctl stop nym-node && \
sudo mv ~/nym/target/release/nym-node $(which nym-node) && \
sudo systemctl start nym-node && sudo journalctl -u nym-node -f -o cat
```

### Change mixnode version to the v1.1.2 in the Nym Wallet (Bonding - Node Settings section)

#

### 🟢 **GW UPDATING**
### Build nym-node
```
cd $HOME
rm -rf nym
git clone https://github.com/nymtech/nym.git
cd nym
git checkout nym-binaries-v2024.5-ragusa
cargo build --release --bin nym-node
```

### Replace and restart
```bash
sudo systemctl stop nym-node && \
sudo mv ~/nym/target/release/nym-node $(which nym-node) && \
sudo systemctl daemon-reload && \
sudo systemctl start nym-node && sudo journalctl -u nym-node -f -o cat
```

### Change version to 1.1.2 in the Nym Wallet
Menu Bonding -> Gateway Settings    
> ![](https://github.com/toolfun/_pics/blob/988df446b0c9c368b68d03503a56b8b74362b505/gwsett.jpg)    
> ![](https://github.com/toolfun/_pics/blob/988df446b0c9c368b68d03503a56b8b74362b505/gwsett2.jpg)    

