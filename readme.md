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
```

```bash
    $ sudo tee /etc/resolver/lc >/dev/null <<EOF
nameserver 127.0.0.1
EOF
```

restart to apply

## using https

go to the root of the repo (`docker-env`) and generate certificates using [mkcert](https://github.com/FiloSottile/mkcert) :

```bash
# If it's the firt install of mkcert, run
mkcert -install

# Generate certificate for domain "docker.localhost", "domain.local" and their sub-domains
mkcert -cert-file certs/local-cert.pem -key-file certs/local-key.pem "docker.localhost" "*.docker.localhost" "domain.lc" "*.domain.lc"
```

## nfs native for macos

### How to enable Mac OS X native NFS

And run app in Docker with native speed

Based on:

- https://medium.com/@sean.handley/how-to-set-up-docker-for-mac-with-native-nfs-145151458adc
- https://apple.stackexchange.com/a/337510

### Prerequisites

Before doing anything check if NFS is already enabled in your system:

```sh
$ sudo nfsd status
```

If you get something like:

```
nfsd service is enabled
nfsd is running (pid 306, 8 threads)
```

Then you have nothing to do here! Otherwise read further.

#### 1) OS X Mojave users only: allow full disk access for your terminal app

1. Go to System Preferences > Security & Privacy > Privacy.
2. Click the "lock" icon to make changes.
3. Scroll down the list on the left-hand side and select "Full Disk Access"
4. Click the "+" icon on the right and select the Terminal app (or in my case iTerm).

#### 2) run setup file on you host machine

```bash
# chmod a+x setup_native_nfs_docker_osx.sh
# ./setup_native_nfs_docker_osx.sh
```

It will stop running Dockers containers and Docker service, enable NFS and then start Docker sevice back. You will be prompted for sudo password when enabling `nfsd` service.

#### 3) Update your `docker-compose.yml` according to the following example:

```yaml
version: "3"
services:
  api:
    volumes:
      - "nfsmount:/container/project/dir"

volumes:
  nfsmount:
    driver: local
    driver_opts:
      type: nfs
      o: addr=host.docker.internal,rw,nolock,hard,nointr,nfsvers=3
      device: ":/my/project/dir"
```

#### 4) Start your Docker project as usual:

```bash
$ docker-compose up
```
