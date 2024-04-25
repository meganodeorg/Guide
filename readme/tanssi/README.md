# Tanssi

#### Website: https://www.tanssi.network

#### Telegram (Tanssi Network Official): https://t.me/tanssiofficial



#### Twitter: https://twitter.com/TanssiNetwork

#### Discord: https://discord.gg/zrHsyVUnrR

#### Docs: https://docs.tanssi.network

#### Explorer: https://polkadot.js.org/apps/?rpc=wss://fraa-dancebox-rpc.a.dancebox.tanssi.network

#### Check telemetry: https://telemetry.polkadot.io/#list/0x27aafd88e5921f5d5c6aebcd728dacbbf5c2a37f63e2eda301f8e0def01c43ea

#### SubScan: https://dancebox.subscan.io

#### Staking: https://apps.tanssi.network/staking

#### Fill form: https://www.tanssi.network/block-producer-form

### Recommended Hardware Requirements

|     SPEC    |   FullNode   | Block Producer |
| :---------: | :----------: | :------------: |
|   **CPU**   |   ≥ 4 Cores  |    ≥ 8 Cores   |
|   **RAM**   |    ≥ 8 GB    |     ≥ 32 GB    |
| **Storage** | ≥ 500 GB SSD |   ≥ 1 TB SSD   |
| **NETWORK** |  ≥ 100 Mbps  |   ≥ 500 Mbps   |

### Update ubuntu and install Docker

```
cd $HOME && source <(curl -s https://raw.githubusercontent.com/vnbnode/binaries/main/docker-install.sh)
```

### Pull image

```
docker pull moondancelabs/tanssi
```

### Run Node

Please change `TANSSI_NAME` to your name

```
docker run --name tanssi --network="host" -d -v "$HOME/dancebox:/data" \
-u $(id -u ${USER}):$(id -g ${USER}) \
moondancelabs/tanssi \
--chain=dancebox \
--name=TANSSI_NAME \
--sync=warp \
--base-path=/data/para \
--state-pruning=2000 \
--blocks-pruning=2000 \
--collator \
--telemetry-url='wss://telemetry.polkadot.io/submit/ 0' \
--database paritydb \
-- \
--name=TANSSI_NAME \
--base-path=/data/container \
--telemetry-url='wss://telemetry.polkadot.io/submit/ 0' \
-- \
--chain=westend_moonbase_relay_testnet \
--name=TANSSI_NAME \
--sync=fast \
--base-path=/data/relay \
--state-pruning=2000 \
--blocks-pruning=2000 \
--telemetry-url='wss://telemetry.polkadot.io/submit/ 0' \
--database paritydb
docker update --restart=unless-stopped tanssi
```

#### Generate Session Keys

```
curl http://127.0.0.1:9944 -H \
"Content-Type:application/json;charset=utf-8" -d \
  '{
    "jsonrpc":"2.0",
    "id":1,
    "method":"author_rotateKeys",
    "params": []
  }'
```

#### Check log node

```
docker logs -f tanssi
```

### Update Node

```
docker stop tanssi
docker rm tanssi
docker pull moondancelabs/tanssi
```

* Please change `TANSSI_NAME` to your name

```
docker run --name tanssi --network="host" -d -v "$HOME/dancebox:/data" \
-u $(id -u ${USER}):$(id -g ${USER}) \
moondancelabs/tanssi \
--chain=dancebox \
--name=TANSSI_NAME \
--sync=warp \
--base-path=/data/para \
--state-pruning=2000 \
--blocks-pruning=2000 \
--collator \
--telemetry-url='wss://telemetry.polkadot.io/submit/ 0' \
--database paritydb \
-- \
--name=TANSSI_NAME \
--base-path=/data/container \
--telemetry-url='wss://telemetry.polkadot.io/submit/ 0' \
-- \
--chain=westend_moonbase_relay_testnet \
--name=TANSSI_NAME \
--sync=fast \
--base-path=/data/relay \
--state-pruning=2000 \
--blocks-pruning=2000 \
--telemetry-url='wss://telemetry.polkadot.io/submit/ 0' \
--database paritydb
docker update --restart=unless-stopped tanssi
```
