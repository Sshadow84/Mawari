# Mawari


![mawari22](https://github.com/user-attachments/assets/879e295f-3800-4199-ba76-2273a547b630)

| X | Minimum |
|---|---|
| **CPU** | 4++ |
| **RAM** | 5++ GB |
| **Disk** | 50 GB+ NVME SSD |
| **Internet Speed** | 100 Mbps (1 Gbps+ recommended) |

---

# Mawari Node — Ubuntu Install Guide (RU / EN)

> **RU | EN** · **Mawari** — децентрализованная сеть (DePIN) для пространственных вычислений и потоковой передачи **ИИ-управляемых 3D-опытов** в реальном времени. Ставка на **edge-ноды** в публичных местах, чтобы снизить задержку и экономить трафик; качество услуг и репутацию обеспечивает слой **Guardian Nodes** (QoS/маршрутизация).  
> **Mawari** is a decentralized (DePIN) network for spatial computing and real-time streaming of **AI-driven 3D experiences**. The focus is on **edge nodes** in public spaces to reduce latency and bandwidth costs; **Guardian Nodes** handle QoS, reputation, and routing.

**Official links:**  
- Website: https://mawari.net/  
- Blog: https://blog.mawari.net/  
- X/Twitter: https://x.com/mawariXR  
- Discord: https://discord.gg/aGRJpjYjRx  
- Telegram: https://t.me/mawarinet

**Funding (community-provided sources):** ~$17.8M total (incl. ~$0.5M via node sale): https://cryptorank.io/ru/ico/mawari  
**Tokenomics:** 4% to community rewards: https://docs.mawari.net/mawari-economy/allocations

---

## 2. Подготовка к установке ноды
### 2.1 Создаём новый кошелёк и получаем на него NFT

1. Установите MetaMask и создайте **новый (burner) кошелёк** для тестнета.
2. Откройте **https://testnet.mawari.net/mint** и **подключите кошелёк**. Если MetaMask предложит **добавить сеть** — подтвердите.
   <img width="1047" height="797" alt="malwari2" src="https://github.com/user-attachments/assets/be6f9735-25d6-4196-bc9b-eb636eb25869" />
3. Если автодобавление не появилось, добавьте сеть вручную:

> **Mawari Network Testnet**
>
> **Chain ID:** `576`  
> **Network name:** Mawari Network Testnet  
> **RPC URL:** https://rpc.testnet.mawari.net/http  
> **Currency symbol:** `MAWARI`  
> **Block explorer URL (optional):** https://explorer.testnet.mawari.net


4. На странице mint нажмите **Faucet Claim** — вы перейдёте на **https://hub.testnet.mawari.net/**.  
   Вставьте адрес нового кошелька и **запросите тестовые токены**.

6. Вернитесь на **https://testnet.mawari.net/mint** и нажмите **Mint** — можно заминтить **до 3 NFT** (если доступно).

<!-- Места под скриншоты (замените пути на свои файлы) -->
<!-- ![Подключение кошелька](assets/connect_wallet.png) -->
<!-- ![Добавление сети](assets/add_network.png) -->
<!-- ![Фаусет](assets/faucet_claim.png) -->
<!-- ![Минт NFT](assets/mint_nft.png) -->


### 1) Минимальные требования
| Ресурс | Минимум |
|---|---|
| CPU | 4+ ядер |
| RAM | 5+ ГБ |
| Диск | 50+ ГБ NVMe SSD |
| Сеть | ≥100 Мбит/с (рекомендуется 1 Гбит/с) |

### 2) Обновление системы и пакеты
```bash
sudo apt update -y && sudo apt upgrade -y
sudo apt install -y htop ca-certificates zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev \
  tmux iptables curl nvme-cli git wget make jq libleveldb-dev build-essential pkg-config \
  ncdu tar clang bsdmainutils lsb-release libssl-dev libreadline-dev libffi-dev gcc screen \
  file unzip lz4
```

> Уже есть Docker/Compose и всё работает? Перейдите к **шагу 4** (группа docker), затем **шаг 5** (переменные) и **шаг 6** (запуск).

### 3) (Опционально) Установка Docker и Docker Compose
```bash
# Docker Engine
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list >/dev/null
sudo apt-get update && sudo apt-get install -y docker-ce docker-ce-cli containerd.io
docker version

# Docker Compose (standalone)
VER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)
curl -L "https://github.com/docker/compose/releases/download/$VER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

### 4) Права для пользователя Docker
```bash
sudo groupadd docker || true
sudo usermod -aG docker $USER
# применить без выхода из SSH-сессии:
newgrp docker
```

### 5) Переменные окружения
```bash
export MNTESTNET_IMAGE=us-east4-docker.pkg.dev/mawarinetwork-dev/mwr-net-d-car-uses4-public-docker-registry-e62e/mawari-node:latest
export OWNER_ADDRESS=0xВАШ_АДРЕС_ИЗ_FAUCET_OR_MINT
```

### 6) Запуск ноды (Docker)
```bash
mkdir -p ~/mawari
docker run -d --pull always --restart unless-stopped \
  -v ~/mawari:/app/cache \
  -e OWNERS_ALLOWLIST=$OWNER_ADDRESS \
  $MNTESTNET_IMAGE
```

### 7) Логи и burner-адрес
```bash
docker ps -a
docker logs -f <CONTAINER_ID>
```
- В логах появится **Burner Wallet Address** — скопируйте его.  
- Отправьте немного тестовых токенов на **burner-адрес** (для комиссий и делегирования).  
- Сообщение `no delegations, skipping heartbeat` **нормально** до момента делегирования.

### 8) Делегирование лицензий (активация)
1) Откройте: `https://app.testnet.mawari.net/licenses`  
2) Отметьте доступные элементы (до 3) → **Delegate**  
3) Вставьте **burner-адрес** → подтвердите транзакцию в кошельке  
4) Статус станет **Active**

### 9) Бэкап приватного ключа burner-кошелька
```bash
cat ~/mawari/flohive-cache.json
```
Сохраните файл/ключ в безопасном месте.

---

## EN — Quick Start on Ubuntu 22.04/24.04

> ⚠️ **Testnet**. Always use a **fresh (burner) wallet**. Testnet tokens have no real value.

### 0) Wallet & Testnet
1. Install and create a new wallet (MetaMask).  
2. Open `https://testnet.mawari.net/mint` — MetaMask should **offer to add the network automatically**.  
3. If you need to **add it manually**, two parameter sets have been seen historically (follow the portal suggestion):
   - **Option A (Caldera Portal):**  
     - Chain ID: `629274`  
     - RPC (HTTP): `https://mawari-network-testnet.rpc.caldera.xyz/http`  
     - Explorer: `https://mawari-network-testnet.explorer.caldera.xyz/`
   - **Option B (Hub/Testnet):**  
     - Chain ID: `576`  
     - RPC (HTTP): `https://rpc.testnet.mawari.net/http`  
     - Explorer: `https://explorer.testnet.mawari.net`
4. Claim test tokens: `https://hub.testnet.mawari.net/`.  
5. (Optional) Mint up to 3 NFTs at `https://testnet.mawari.net/mint` (if available).

### 1) Minimum server requirements
| Resource | Minimum |
|---|---|
| CPU | 4+ cores |
| RAM | 5+ GB |
| Disk | 50+ GB NVMe SSD |
| Network | ≥100 Mbps (1 Gbps recommended) |

### 2) Update system & packages
```bash
sudo apt update -y && sudo apt upgrade -y
sudo apt install -y htop ca-certificates zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev \
  tmux iptables curl nvme-cli git wget make jq libleveldb-dev build-essential pkg-config \
  ncdu tar clang bsdmainutils lsb-release libssl-dev libreadline-dev libffi-dev gcc screen \
  file unzip lz4
```

> If Docker/Compose is already installed and working, **skip to Step 4**, then do **Step 5** and **Step 6**.

### 3) (Optional) Install Docker & Docker Compose
```bash
# Docker Engine
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list >/null
sudo apt-get update && sudo apt-get install -y docker-ce docker-ce-cli containerd.io
docker version

# Docker Compose (standalone)
VER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)
curl -L "https://github.com/docker/compose/releases/download/$VER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

### 4) Docker user group
```bash
sudo groupadd docker || true
sudo usermod -aG docker $USER
newgrp docker
```

### 5) Environment variables
```bash
export MNTESTNET_IMAGE=us-east4-docker.pkg.dev/mawarinetwork-dev/mwr-net-d-car-uses4-public-docker-registry-e62e/mawari-node:latest
export OWNER_ADDRESS=0xYOUR_FAUCET_OR_MINT_ADDRESS
```

### 6) Run the node (Docker)
```bash
mkdir -p ~/mawari
docker run -d --pull always --restart unless-stopped \
  -v ~/mawari:/app/cache \
  -e OWNERS_ALLOWLIST=$OWNER_ADDRESS \
  $MNTESTNET_IMAGE
```

### 7) Logs & burner address
```bash
docker ps -a
docker logs -f <CONTAINER_ID>
```
- Copy the **Burner Wallet Address** from logs.  
- Send some test tokens to the **burner** wallet for gas & delegations.  
- Seeing `no delegations, skipping heartbeat` is **normal** until you delegate.

### 8) Delegate licenses (activate)
`https://app.testnet.mawari.net/licenses` → select available items (up to 3) → **Delegate** → paste **burner address** → confirm in wallet → status becomes **Active**.

### 9) Backup burner private key
```bash
cat ~/mawari/flohive-cache.json
```
Store safely.

---

## (Optional) docker-compose variant

Create **docker-compose.yml**:
```yaml
services:
  mawari-node:
    image: us-east4-docker.pkg.dev/mawarinetwork-dev/mwr-net-d-car-uses4-public-docker-registry-e62e/mawari-node:latest
    container_name: mawari-node
    restart: unless-stopped
    environment:
      - OWNERS_ALLOWLIST=${OWNER_ADDRESS}
    volumes:
      - ./mawari:/app/cache
```

Create **.env**:
```
OWNER_ADDRESS=0xYOUR_FAUCET_OR_MINT_ADDRESS
```

Run:
```bash
mkdir -p ~/mawari-node && cd ~/mawari-node
mkdir -p mawari
# save docker-compose.yml and .env here
docker compose up -d
```

