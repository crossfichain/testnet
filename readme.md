## Sync node

### From snapshot (fast)

1. Download binary
```bash
wget https://github.com/crossfichain/crossfi-node/releases/download/v0.3.0-prebuild9/crossfi-node_0.3.0-prebuild9_linux_amd64.tar.gz && tar -xf crossfi-node_0.3.0-prebuild9_linux_amd64.tar.gz
```

2. Download configs
```bash
git clone https://github.com/crossfichain/testnet.git
```

3. Start node
```bash
./bin/crossfid start --home ./testnet
```

### Archive

1. Download binary
```bash
wget https://github.com/crossfichain/crossfi-node/releases/download/v0.2.0-prebuild6/crossfi-node_0.2.0-prebuild6_linux_amd64.tar.gz && tar -xf crossfi-node_0.2.0-prebuild6_linux_amd64.tar.gz
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

## Run a validator

1. Sync your node using instruction above
2. Create your key by running `./bin/crossfid --home ./testnet keys add my_validator`. If you already have a wallet, you can recover with 
`./bin/crossfid --home ./testnet keys add my_validator --recover` then you can paste your mnemonics.
3. Get address from commands output and top in up with some amount of MPX
4. Run transaction to create a validator:
```bash
./bin/crossfid --home ./testnet tx staking create-validator \
  --amount=1000000000000000000mpx \
  --pubkey=$(./bin/crossfid --home ./testnet tendermint show-validator) \
  --moniker="My Fist Validator" \
  --chain-id="crossfi-evm-testnet-1" \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1000000" \
  --gas="auto" \
  --gas-prices="10000000000000mpx" \
  --gas-adjustment=1.5 \
  --from=my_validator
```
5. Check out your validator in https://test.xfiscan.com/
