Welcome To The Installation

# First We Update The System

```
sudo apt update
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y
```
# We Are Installing #GO

```
sudo rm -rf /usr/local/go
curl -L https://go.dev/dl/go1.21.6.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile
source .bash_profile
```

# Binary 

```
cd $HOME
mkdir -p $HOME/go/bin
wget https://github.com/crossfichain/crossfi-node/releases/download/v0.3.0-prebuild3/crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz && tar -xf crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz
tar -xvf crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz
chmod +x $HOME/bin/crossfid
mv $HOME/bin/crossfid $HOME/go/bin
rm -rf crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz $HOME/bin
```

# Init Process

```
crossfid config chain-id crossfi-evm-testnet-1
crossfid config keyring-backend test
crossfid config node tcp://localhost:26657
git clone https://github.com/crossfichain/testnet.git
mv $HOME/testnet/ $HOME/.mineplex-chain/
```

# Change My_Validator To Your Own

```
crossfid init "My_Validator" --chain-id crossfi-evm-testnet-1
```

# Pulling Genesis and Addrbook Files

```
wget -O $HOME/.mineplex-chain/config/genesis.json https://testnet-files.itrocket.net/crossfi/genesis.json
wget -O $HOME/.mineplex-chain/config/addrbook.json https://testnet-files.itrocket.net/crossfi/addrbook.json
```

# Pruning Commands (OPTIONAL)

```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.mineplex-chain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.mineplex-chain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.mineplex-chain/config/app.toml
```

# We Add Seed and Peer

```
SEEDS="dd83e3c7c4e783f8a46dbb010ec8853135d29df0@crossfi-testnet-seed.itrocket.net:36656"
PEERS="66bdf53ec0c2ceeefd9a4c29d7f7926e136f114a@crossfi-testnet-peer.itrocket.net:36656,5ebd3b1590d7383c0bb6696ad364934d7f1c984e@160.202.128.199:56156,b88d969ba0e158da1b4066f5c17af9da68c52c7a@65.109.53.24:44656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.mineplex-chain/config/config.toml
```

# Minimum Gas Setting

```
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "10000000000000mpx"|g' $HOME/.mineplex-chain/config/app.toml
```

# We Create a Service File

```
sudo tee /etc/systemd/system/crossfid.service > /dev/null << EOF
[Unit]
Description=CrossFi Node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.mineplex-chain
ExecStart=$(which crossfid) start --home $HOME/.mineplex-chain
Restart=on-failure
RestartSec=5
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
EOF
```

# Snapshot

```
crossfid tendermint unsafe-reset-all --home $HOME/.mineplex-chain
if curl -s --head curl https://testnet-files.itrocket.net/crossfi/snap_crossfi.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://testnet-files.itrocket.net/crossfi/snap_crossfi.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.mineplex-chain
    else
  echo no have snap
fi
```

# We Activate the Service File and Monitor the Logs

```
sudo systemctl daemon-reload
sudo systemctl enable crossfid
sudo systemctl start crossfid && sudo journalctl -u crossfid -f
```
# We Are Looking At the Node Sync Status. We Should See False Output

```
curl -s localhost:26657/status | jq .result.sync_info
```

# Wallet Import 

```
crossfid keys add wallet --recover
```

# Create Validator

```
crossfid tx staking create-validator \
--amount=1000000mpx \
--pubkey=$(crossfid tendermint show-validator) \
--moniker="MONIKER_ADI" \
--chain-id=crossfi-evm-testnet-1 \
--commission-rate=0.10 \
--commission-max-rate=0.20 \
--commission-max-change-rate=0.01 \
--min-self-delegation=1 \
--from=wallet \
--gas-prices=10000000000000mpx \
--gas-adjustment=1.5 \
--gas=auto \
-y 
```

# Some Commands

```
crossfid keys .....
crossfid tx .....
```

