## Run Multi Synchronizer on Single VPS

### Step:1 Run synchronizer init and input a **new key and wallet** for each instance
```
synchronizer init
```
### Step:2 Pull Docker Image
```
mkdir -p /root/.synchronizer-cli-instance-01
cp -r /root/.synchronizer-cli/* /root/.synchronizer-cli-instance-01/
```
### Step:3 REDO THE PROCESS, depends on how many instance you are going to make. 
```
synchronizer init
mkdir -p /root/.synchronizer-cli-instance-02
cp -r /root/.synchronizer-cli/* /root/.synchronizer-cli-instance-02/

synchronizer init
mkdir -p /root/.synchronizer-cli-instance-03
cp -r /root/.synchronizer-cli/* /root/.synchronizer-cli-instance-03/
```

----------------------------------------------------------
# Ensure different host ports for each instance to prevent conflicts
----------------------------------------------------------

nano /etc/systemd/system/synchronizer-cli-02.service
```
[Unit]
Description=Synchronizer Instance 01
After=docker.service
Requires=docker.service

[Service]
Type=simple
User=root
Restart=always
RestartSec=10
ExecStart=/usr/bin/docker run --rm --name synchronizer-cli-01 --pull always \
  --platform linux/amd64 \
  -e HTTPS_PROXY=http://proxy1.example.com:3128 \
  -v /root/.synchronizer-cli-instance-01/config.json:/app/config.json \
  -p 3333:3333 \
  cdrakep/synqchronizer:latest \
  --depin wss://api.multisynq.io/depin \
  --sync-name synq-XXXXXXXXX \
  --launcher cli-2.6.1/docker-2025-06-07 \
  --key <KEY_01> \
  --wallet <WALLET_01>

[Install]
WantedBy=multi-user.target
```


nano /etc/systemd/system/synchronizer-cli-03.service
```

[Unit]
Description=Synchronizer Instance 03
After=docker.service
Requires=docker.service

[Service]
Type=simple
User=root
Restart=always
RestartSec=10
ExecStart=/usr/bin/docker run --rm --name synchronizer-cli-03 --pull always \
  --platform linux/amd64 \
  -e HTTPS_PROXY=http://proxy1.example.com:3128 \
  -v /root/.synchronizer-cli-instance-03/config.json:/app/config.json \
  -p 3335:3333 \
  cdrakep/synqchronizer:latest \
  --depin wss://api.multisynq.io/depin \
  --sync-name synq-XXXXXXXXX \
  --launcher cli-2.6.1/docker-2025-06-07 \
  --key <KEY_01> \
  --wallet <WALLET_01>

[Install]
WantedBy=multi-user.target
```

nano /etc/systemd/system/synchronizer-cli-03.service

```
[Unit]
Description=Synchronizer Instance 03
After=docker.service
Requires=docker.service

[Service]
Type=simple
User=root
Restart=always
RestartSec=10
ExecStart=/usr/bin/docker run --rm --name synchronizer-cli-03 --pull always \
  --platform linux/amd64 \
  -e HTTPS_PROXY=http://proxy1.example.com:3128 \
  -v /root/.synchronizer-cli-instance-03/config.json:/app/config.json \
  -p 3335:3333 \
  cdrakep/synqchronizer:latest \
  --depin wss://api.multisynq.io/depin \
  --sync-name synq-XXXXXXXXX \
  --launcher cli-2.6.1/docker-2025-06-07 \
  --key <KEY_01> \
  --wallet <WALLET_01>

[Install]
WantedBy=multi-user.target
```

```
sudo systemctl daemon-reload
sudo systemctl enable synchronizer-cli-01
sudo systemctl start synchronizer-cli-01
```

```
sudo systemctl enable synchronizer-cli-02
sudo systemctl start synchronizer-cli-02
```

```
sudo systemctl enable synchronizer-cli-03
sudo systemctl start synchronizer-cli-03
```


- CHECK IF YOUR DOCKER IS RUNNING ON PROXY
docker exec synchronizer-cli-01 curl -v https://api.ipify.org
docker exec synchronizer-cli-02 curl -v https://api.ipify.org
docker exec synchronizer-cli-03 curl -v https://api.ipify.org
