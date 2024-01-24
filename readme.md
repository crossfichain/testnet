### Sync from snapshot (fast)

1. Download binary
```bash
wget https://github.com/crossfichain/crossfi-node/releases/download/v0.3.0-prebuild3/crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz && tar -xf crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz
```

2. Download configs
```bash
git clone https://github.com/crossfichain/testnet.git
```

3. Start node
```bash
./bin/crossfid start --home ./testnet
```

### Archive node sync

1. Download binary
```bash
wget https://github.com/crossfichain/crossfi-node/releases/download/v0.3.0-prebuild1/crossfi-node_0.3.0-prebuild1_linux_amd64.tar.gz && tar -xf crossfi-node_0.3.0-prebuild1_linux_amd64.tar.gz
```

2. Download and edit configs
```bash
git clone https://github.com/crossfichain/testnet.git
```

Edit testnet/config/config.toml: set `enabled = false` in `[statesync]` section.

3. Start node
```bash
./bin/crossfid start --home ./testnet
```