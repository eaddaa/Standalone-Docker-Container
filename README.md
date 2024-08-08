# Standalone-Docker-Container

```
sudo apt-get update && sudo apt-get upgrade -y

sudo apt install apt-transport-https ca-certificates curl software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update

sudo apt install docker-ce docker-ce-cli containerd.io

sudo systemctl start docker
sudo systemctl enable docker

docker --version

sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

docker-compose --version

sudo apt install git
sudo apt install nodejs
sudo apt install npm

git clone https://github.com/powerpool-finance/powerpool-agent-v2-compose
cd powerpool-agent-v2-compose

npm i
node jsongen.js ${YOUR_WORKER_PRIVATE_KEY} ${A_PASSWORD_OF_YOUR_CHOICE}

nano config/main.template.yaml

mv config/main.template.yaml config/main.yaml

# Launch the node

NODE_ENV=dev docker compose --profile latest up -d

```
Update
```
git pull
docker compose pull
docker pull powerpool/power-agent-node:latest
docker pull powerpool/power-agent-offchain-service:latest
```

Usefull commands
```
docker stop $(docker ps -aq)

docker restart $(docker ps -aq)

sudo systemctl restart docker

docker logs --tail 10 powerpool-agent-v2-compose-agent-latest-1

docker logs powerpool-agent-v2-compose-agent-latest-1

docker logs powerpool-agent-v2-compose-temp-executor-1

docker logs powerpool-agent-v2-compose-offchain-service-1      
   
docker restart powerpool-agent-v2-compose-temp-executor-1


Stop
docker compose --profile latest down

Start
NODE_ENV=dev docker compose --profile latest up -d

```
diffrent networks
```
git clone https://github.com/powerpool-finance/powerpool-agent-v2-compose
cp -r powerpool-agent-v2-compose powerpool-agent-base
cp -r powerpool-agent-v2-compose powerpool-agent-arbitrum
```
```
cd powerpool-agent-base
npm i
node jsongen.js ${YOUR_BASE_WORKER_PRIVATE_KEY} ${A_PASSWORD_OF_YOUR_CHOICE}
```
```
cd ../powerpool-agent-arbitrum
npm i
node jsongen.js ${YOUR_ARBITRUM_WORKER_PRIVATE_KEY} ${A_PASSWORD_OF_YOUR_CHOICE}

```

powerpool-agent-arbitrum/docker-compose.yaml
check logs
```
docker logs --tail 100 -f powerpool-agent-arbitrum-agent-dev-1

```
NODE_API_PORT: 9100
OFFCHAIN_API_PORT: 3523

 ```
version: "3.9"

services:
  agent-dev:
    image: powerpool/power-agent-node:dev
    restart: always
    volumes:
      - ./config:/usr/app/config
      - ./keys:/usr/app/keys
    ports:
      - "${NODE_API_PORT:-9100}:${NODE_API_PORT:-9100}"
    environment:
      NODE_ENV: ${NODE_ENV}
      API_PORT: ${NODE_API_PORT:-9100}
      OFFCHAIN_SERVICE_ENDPOINT: http://offchain-service:${OFFCHAIN_API_PORT:-3523}
    profiles: [dev]

  offchain-service:
    image: powerpool/power-agent-offchain-service:latest
    restart: always
    volumes:
      - ./scriptsFetched:/scriptsFetched
      - ./scriptToExecute:/scriptToExecute
      - /var/run/docker.sock:/var/run/docker.sock
    ports:
      - "${OFFCHAIN_API_PORT:-3523}:${OFFCHAIN_API_PORT:-3523}"
    environment:
      NODE_ENV: ${NODE_ENV}
      API_PORT: ${OFFCHAIN_API_PORT:-3523}
      AGENT_API_PORT: ${NODE_API_PORT:-9100}
      DOCKER_MODE: 1
      MAX_EXECUTION_TIME: ${OFFCHAIN_MAX_EXECUTION_TIME}
    profiles: [dev]

  temp-executor:
    image: node:18-alpine
    restart: "no"
    command: ["echo", "This is a temporary executor"]
    profiles: [dev]


```
launch node

```
cd ~/powerpool-agent-arbitrum
NODE_ENV=dev NODE_API_PORT=9100 OFFCHAIN_API_PORT=3523 OFFCHAIN_MAX_EXECUTION_TIME=300 docker compose --profile dev up -d
```



powerpool-agent-v2-compose/docker-compose.yaml
check logs
```
docker logs --tail 100 -f powerpool-agent-v2-compose-agent-latest-1

```

NODE_API_PORT: 8199
OFFCHAIN_API_PORT: 3424

```
version: "3.9"

services:
  agent-latest:
    image: powerpool/power-agent-node:latest
    restart: always
    volumes:
      - ./config:/usr/app/config
      - ./keys:/usr/app/keys
    ports:
      - "${NODE_API_PORT:-8199}:${NODE_API_PORT:-8199}"
    environment:
      NODE_ENV: ${NODE_ENV}
      API_PORT: ${NODE_API_PORT:-8199}
      OFFCHAIN_SERVICE_ENDPOINT: http://offchain-service:${OFFCHAIN_API_PORT:-3424}
    profiles: [latest]

  offchain-service:
    image: powerpool/power-agent-offchain-service:latest
    restart: always
    volumes:
      - ./scriptsFetched:/scriptsFetched
      - ./scriptToExecute:/scriptToExecute
      - /var/run/docker.sock:/var/run/docker.sock
    ports:
      - "${OFFCHAIN_API_PORT:-3424}:${OFFCHAIN_API_PORT:-3424}"
    environment:
      NODE_ENV: ${NODE_ENV}
      API_PORT: ${OFFCHAIN_API_PORT:-3424}
      AGENT_API_PORT: ${NODE_API_PORT:-8199}
      DOCKER_MODE: 1
      MAX_EXECUTION_TIME: ${OFFCHAIN_MAX_EXECUTION_TIME}
    profiles: [latest]

  temp-executor:
    image: node:18-alpine
    restart: "no"
    command: ["echo", "This is a temporary executor"]
    profiles: [latest]


```
launch node

```
cd ~/powerpool-agent-v2-compose
NODE_ENV=dev NODE_API_PORT=8199 OFFCHAIN_API_PORT=3424 OFFCHAIN_MAX_EXECUTION_TIME=300 docker compose --profile latest up -d

```
