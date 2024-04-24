# Command

### Managing keys

Generate new key

```
sided keys add wallet

```

Recover key

```
sided keys add wallet --recover

```

List all key

```
sided keys list

```

Delete key

```
sided keys delete wallet
```

Query wallet balances

```
alignedlayerd q bank balances $(alignedlayerd keys show wallet -a)
```

### Managing validators

Create validator

```
sided tx staking create-validator \
--amount 1000000uside \
--pubkey $(sided tendermint show-validator) \
--moniker "your-moniker-name" \
--identity "your-keybase-id" \
--details "your-details" \
--website "your-website" \
--security-contact "your-email" \
--chain-id side-testnet-2 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.005uside \
-y
```

Edit validator

```
sided tx staking edit-validator \
--new-moniker "your-moniker-name" \
--identity "your-keybase-id" \
--details "your-details" \
--website "your-website" \
--security-contact "your-email" \
--chain-id side-testnet-2 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.005uside \
-y
```

Unjail

```
sided tx slashing unjail --from wallet --chain-id side-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.005uside -y

```

Jail reason

```
sided query slashing signing-info $(sided tendermint show-validator)
```

View validator details

```
sided q staking validator $(sided keys show wallet --bech val -a)

```

Query active validators

```
sided q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

Query inactive validators

```
sided q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

### Managing Tokens

Delegate tokens to your validator

```
sided tx staking delegate $(sided keys show wallet --bech val -a) 1000000uside --from wallet --chain-id side-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.005uside -y
```

Send token

```
sided tx bank send wallet <to-wallet-address> 1000000uside --from wallet --chain-id side-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.005uside -y
```

Withdraw reward from all validator

```
sided tx distribution withdraw-all-rewards --from wallet --chain-id side-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.005uside -y
```

Withdraw reward and commission

```
sided tx distribution withdraw-rewards $(sided keys show wallet --bech val -a) --commission --from wallet --chain-id side-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.005uside -y
```

Redelegate to another validator

```
sided tx staking redelegate $(sided keys show wallet --bech val -a) <to-valoper-address> 1000000uside --from wallet --chain-id side-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.005uside -y
```

### Governance

Query list proposal

```
sided query gov proposals
```

View proposal by ID

```
sided query gov proposal 1
```

Vote yes

```
sided tx gov vote 1 yes --from wallet --chain-id side-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.005uside -y
```

Vote No

```
sided tx gov vote 1 no --from wallet --chain-id side-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.005uside -y
```

Vote option NoWithVeto

```
sided tx gov vote 1 NoWithVeto --from wallet --chain-id side-testnet-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.005uside -y
```

### Maintenance

Check sync

```
sided status 2>&1 | jq .SyncInfo
```

Node status

```
sided status 2>&1 | jq
```

Get validator information

```
alignedlayerd status 2>&1 | jq .ValidatorInfo
```

Get your p2p peer address

```
echo $(sided tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.side/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

Get peers live

```
curl -sS http://localhost:23857/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

Remove node

```
cd $HOME
sudo systemctl stop seda
sudo systemctl disable seda
sudo rm /etc/systemd/system/seda.service
sudo systemctl daemon-reload
sudo rm -f $(which sided)
sudo rm -rf $HOME/.side
sudo rm -rf $HOME/seda-chain
sudo rm -rf $HOME/go
```
