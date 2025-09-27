# Mawari

<img src="https://github.com/user-attachments/assets/879e295f-3800-4199-ba76-2273a547b630" alt="Mawari Seed+ $10.8M" width="100%">

---

# Mawari Node — Ubuntu Install Guide (RU)

> **Mawari** — децентрализованная сеть (DePIN) для пространственных вычислений и потоковой передачи **ИИ-управляемых 3D-опытов** в реальном времени. Ставка на **edge-ноды** в публичных местах, чтобы снизить задержку и экономить трафик; качество услуг и репутацию обеспечивает слой **Guardian Nodes** (QoS/маршрутизация).

**Официальные ссылки**
- Website: https://mawari.net/
- Blog: https://blog.mawari.net/
- X/Twitter: https://x.com/mawariXR
- Discord: https://discord.gg/aGRJpjYjRx
- Telegram: https://t.me/mawarinet

**Доп. сведения**
- Funding (community): ~$17.8M total (включая ~$0.5M через node sale): https://cryptorank.io/ru/ico/mawari
- Tokenomics: 4% на community rewards: https://docs.mawari.net/mawari-economy/allocations

---

## 1. Подготовка к установке ноды

### 1.1 Создаём новый кошелёк и получаем на него NFT

1. Установите MetaMask и создайте **новый (burner) кошелёк** для тестнета.
2. Откройте **https://testnet.mawari.net/mint** и **подключите кошелёк**. Если MetaMask предложит **добавить сеть** — подтвердите.

<img width="1047" height="797" alt="Подключение кошелька" src="https://github.com/user-attachments/assets/be6f9735-25d6-4196-bc9b-eb636eb25869" />

Если автодобавление не появилось, добавьте сеть вручную:

> [!NOTE]
> **Mawari Network Testnet**  
> **Chain ID:** `576`  
> **Network name:** Mawari Network Testnet  
> **RPC URL:** https://rpc.testnet.mawari.net/http  
> **Currency symbol:** `MAWARI`  
> **Block explorer URL (optional):** https://explorer.testnet.mawari.net

3. На странице mint нажмите **Faucet Claim** — вы перейдёте на **https://hub.testnet.mawari.net/**. Вставьте адрес нового кошелька и **запросите тестовые токены**.

<img width="1015" height="634" alt="Фаусет" src="https://github.com/user-attachments/assets/b610c050-9e70-45b2-8827-7521079a71d0" />

4. Вернитесь на **https://testnet.mawari.net/mint** и нажмите **Mint** — можно заминтить **до 3 NFT** (если доступно).

<img width="1028" height="774" alt="Минт NFT" src="https://github.com/user-attachments/assets/7ad28336-1346-4bf6-8668-24935b23ea23" />

### 1.2 Минимальные требования
| Ресурс | Минимум |
|---|---|
| CPU | 4+ ядер |
| RAM | 5+ ГБ |
| Диск | 50+ ГБ NVMe SSD |
| Сеть | ≥100 Мбит/с (рекомендуется 1 Гбит/с) |

---

## 2. Установка ноды

### 2.1 Обновление и зависимые пакеты (Ubuntu 22.04/24.04)
```bash
sudo apt update -y && sudo apt upgrade -y
sudo apt install -y htop ca-certificates zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev   tmux iptables curl nvme-cli git wget make jq libleveldb-dev build-essential pkg-config   ncdu tar clang bsdmainutils lsb-release libssl-dev libreadline-dev libffi-dev gcc screen   file unzip lz4
```

> [!TIP]
> Если Docker/Compose уже стоят и работают — переходите сразу к **шагу 2.3** (права), затем **2.4** (переменные) и **2.5** (запуск).

### 2.2 (опционально) Установка Docker и Docker Compose

**Docker Engine**
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list >/dev/null
sudo apt-get update && sudo apt-get install -y docker-ce docker-ce-cli containerd.io
docker version
```

**Docker Compose (standalone)**
```bash
VER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)
curl -L "https://github.com/docker/compose/releases/download/$VER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

### 2.3 Права для пользователя Docker
```bash
sudo groupadd docker || true
sudo usermod -aG docker $USER
# применить без выхода из SSH:
newgrp docker
```

### 2.4 Переменные окружения
```bash
export MNTESTNET_IMAGE=us-east4-docker.pkg.dev/mawarinetwork-dev/mwr-net-d-car-uses4-public-docker-registry-e62e/mawari-node:latest
export OWNER_ADDRESS=0xВАШ_АДРЕС_ИЗ_FAUCET_OR_MINT
```

### 2.5 Запуск ноды (Docker)
```bash
mkdir -p ~/mawari
docker run -d --pull always --restart unless-stopped   -v ~/mawari:/app/cache   -e OWNERS_ALLOWLIST=$OWNER_ADDRESS   $MNTESTNET_IMAGE
```

### 2.6 Проверка логов и burner-адрес
```bash
docker ps -a
docker logs -f <CONTAINER_ID>
```
- В логах появится **Burner Wallet Address** — скопируйте его.  
- Отправьте часть testnet-токенов на **burner-адрес** (для комиссий и делегирования).  
- Сообщение `no delegations, skipping heartbeat` — **нормально** до делегирования.

### 2.7 Делегирование лицензий (активация)
Зайдите на страницу лицензий и делегируйте на burner-адрес:  
`https://app.testnet.mawari.net/licenses` → выберите доступные (до 3) → **Delegate** → вставьте burner-адрес → подтвердите в кошельке. Статус станет **Active**.

### 2.8 Бэкап приватного ключа burner-кошелька
```bash
cat ~/mawari/flohive-cache.json
```
Сохраните файл/ключ в безопасном месте.

### 2.9 Полезные команды
```bash
docker logs -f <ID>                 # логи
docker restart <ID>                 # перезапуск
docker stop <ID> && docker rm <ID>  # пересоздать контейнер
docker pull $MNTESTNET_IMAGE        # обновить образ
```
