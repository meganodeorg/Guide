# SIDE
Chain ID: `side-testnet-3`

## Recommended Hardware Requirements

|   SPEC      |       Recommend          |
| :---------: | :-----------------------:|
|   **CPU**   |        4 Cores           |
|   **RAM**   |        8 GB              |
|   **SSD**   |        500 GB            |
| **NETWORK** |        1 Gbps            |

### Update and install packages for compiling
```
sudo apt update && sudo apt install curl -y
sudo apt install curl tar wget clang pkg-config protobuf-compiler libssl-dev jq build-essential protobuf-compiler bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
wget https://go.dev/dl/go1.22.2.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.22.2.linux-amd64.tar.gz
##Add /usr/local/go/bin to the PATH environment variable##
export PATH=$PATH:/usr/local/go/bin
go version

```

### Install Node
```
cd $HOME
rm -rf sidechain
git clone -b v0.7.0 https://github.com/sideprotocol/sidechain.git
cd sidechain
git checkout v0.7.0
make install
sided version
```

### Cosmovisor Setup
```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
sudo tee /etc/systemd/system/side.service > /dev/null << EOF
[Unit]
Description=Side node service
After=network-online.target
 
[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.side"
Environment="DAEMON_NAME=sided"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.side/cosmovisor/current/bin"
 
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable side
```

### Initialize Node
```
sided config chain-id side-testnet-3	
sided config keyring-backend test
sided config node tcp://localhost:26657
```
```
sided init <Your Node Name> --chain-id=side-testnet-3
```

### Download Genesis & Addrbook
```
curl -Ls https://testnet-files.itrocket.net/side/genesis.json > $HOME/.side/config/genesis.json
curl -Ls https://testnet-files.itrocket.net/side/addrbook.json > $HOME/.side/config/addrbook.json
```

### Configure
```
sed -i -e "s|^seeds *=.*|seeds = \"9c14080752bdfa33f4624f83cd155e2d3976e303@side-testnet-seed.itrocket.net:45656\"|" $HOME/.side/config/config.toml
sed -i -e 's|^persistent_peers *=.*|persistent_peers ="bbbf623474e377664673bde3256fc35a36ba0df1@side-testnet-peer.itrocket.net:45656,6decdc5565bf5232cdf5597a7784bfe828c32277@158.220.126.137:11656,e9ee4fb923d5aab89207df36ce660ff1b882fc72@136.243.33.177:21656,169332e1a5aad8e49fced765992201774a754cd0@95.216.27.29:34656,2a6d31c23160e49db1f03a884dc7b9602fffe895@176.9.126.85:30004,b588e261519d49e436fc503af5b602810110bd36@194.163.149.7:26656,ca3379b48e196c3ef910a08452b459b0f327fdb6@95.216.3.115:34656,2780ffa710b0d42dacc4eeffb4c6bc145ef6636f@38.129.16.236:26656,162c0fffde8769b85fa84db97bb136b1016c0c83@38.242.205.192:26656,53e164d1b28ba845da0cec828b4f69fe1e8bf78a@65.108.153.66:26656,64bc7a0fb50832ff70b11d633038486c912d5220@170.64.163.55:26656"|' $HOME/.side/config/config.toml
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.0001stake\"|" $HOME/.side/config/app.toml
sed -i \
  -e 's|^chain-id *=.*|chain-id = "side-testnet-3"|' \
  -e 's|^keyring-backend *=.*|keyring-backend = "test"|' \
  -e 's|^node *=.*|node = "tcp://localhost:26657"|' \
  $HOME/.side/config/client.toml
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.side/config/app.toml
```

### Custom Port
```
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:36658\"%proxy_app = \"tcp://0.0.0.0:36658\"%; s%^laddr = \"tcp://127.0.0.1:36657\"%laddr = \"tcp://0.0.0.0:36657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:3060\"%; s%^laddr = \"tcp://0.0.0.0:36656\"%laddr = \"tcp://0.0.0.0:36656\"%; s%^prometheus_listen_addr = \":3060\"%prometheus_listen_addr = \":36656\"%" $HOME/.side/config/config.toml
sed -i -e "s%^address = \"tcp://localhost:1317\"%address = \"tcp://0.0.0.0:24217\"%; s%^address = \":8080\"%address = \":24280\"%; s%^address = \"localhost:9090\"%address = \"0.0.0.0:24290\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:24291\"%; s%:8545%:24245%; s%:8546%:24246%; s%:6065%:24265%" $HOME/.side/config/app.toml
```

### Snapshot
```
cp $HOME/.side/data/priv_validator_state.json $HOME/.side/priv_validator_state.json.backup
rm -rf $HOME/.side/data && mkdir -p $HOME/.side/data
curl https://testnet-files.itrocket.net/side/snap_side.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.side
mv $HOME/.side/priv_validator_state.json.backup $HOME/.side/data/priv_validator_state.json
```

### Start Node
```
sudo systemctl start side
journalctl -fu side -o cat
```

### Backup Validator
```
mkdir -p $HOME/backup/side
cp $HOME/.side/config/priv_validator_key.json $HOME/backup/side
```

### Remove Node
```
cd $HOME
sudo systemctl stop side
sudo systemctl disable side
sudo rm /etc/systemd/system/side.service
sudo systemctl daemon-reload
sudo rm -f $(which side)
sudo rm -rf $HOME/.side
sudo rm -rf $HOME/sidechain
```

## Thank to support.
