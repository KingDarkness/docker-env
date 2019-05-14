# setup global swarm
```bash
    $ docker swarm init
```

2. create swarm `nginx-proxy` network
```bash
    $ docker network create -d overlay --attachable nginx-proxy
```

3. deploy on swarm
```bash
    $ docker stack deploy -c docker-compose.yml proxy
```

4. install dnsmasq
## linux
```bash
    $ sudo apt install dnsmasq
```
## mac
```
$ brew install dnsmasq
```

5. config dnsmasq 
## linux
```bash
    $ sudo echo "address=/.lc/127.0.0.1" > /etc/dnsmasq.d/docker
```
edit file `/etc/resolvconf/resolv.conf.d/tail`
add this conf to bottom
```conf
nameserver 8.8.8.8
nameserver 8.8.4.4
```
restart the dnsmasq service 
```bash
    $ sudo service  dnsmasq restart
```
## mac
edit file `/usr/local/etc/dnsmasq.conf`
add this conf to bottom

```conf
address=/.lc/127.0.0.1
listen-address=127.0.0.1
```
```bash
sudo tee /etc/resolver/ls >/dev/null <<EOF
nameserver 127.0.0.1
EOF
```

restart to apply