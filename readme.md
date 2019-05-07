# setup global swarm
```bash
    $ docker swarm init
```
2. create swarm `nginx-proxy` network
```bash
    $ docker network create -d overlay --attachable nginx-proxy
```%
