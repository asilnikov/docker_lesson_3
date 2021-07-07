#Bridge type networks

# look networks
docker network ls

# inspect network
docker network inspect bridge

# network create
docker network create --driver bridge --subnet 192.168.100.0/24 --ip-range 192.168.100.0/24 test-bridge-network
docker network inspect test-bridge-network

# Bridge interfaces in Linux
ifconfig docker0
ifconfig | grep 192.168.100. -B 1
brctl show 

#Linux virtual interfaces

# Container Networking Model
docker container run -ti nginx /bin/bash

# Docker container
apt update
apt install net-tool
ifconfig

# interface and tcpdump 
x2 docker container run -d nginx
x2 docker container run -d --network test-bridge-network nginx
docker ps
brctl show 
docker exec e26cd42fbed6 apt update
docker exec e26cd42fbed6 apt install iputils-ping -y
docker exec e26cd42fbed6 ping google.com
brctl show 
sudo tcpdump -i veth27e2cee icmp

# ping containers 
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' e26cd42fbed6
docker exec 7536001c32c ping 192.168.100.3
sudo tcpdump -i veth27e2cee icmp

#iptables

# iptables:filter

sudo iptables -L -v

# icc=false

docker network create --driver bridge --subnet 192.168.200.0/24 --ip-range 192.168.200.0/24 -o "com.docker.network.bridge.enable_icc"="false" no-icc-network
ifconfig | grep 192.168.200. -B 1
sudo iptables -t filter -S FORWARD

# iptables:nat

sudo iptables -t nat -L