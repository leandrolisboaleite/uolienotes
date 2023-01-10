# Docker + docker compose install script

```bash
#Instalacao Docker
sudo curl -fsSL https://get.docker.com/ | sh
sudo systemctl enable docker
sudo systemctl start docker
sudo systemctl status docker

#Instalacao docker-compose
curl -L https://github.com/docker/compose/releases/download/1.27.4/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

docker --version
```
