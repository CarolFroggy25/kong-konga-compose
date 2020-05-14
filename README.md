# kongpose

  * Preparation
  ```bash
   systemctl stop firewalld
   systemctl disable firewalld
   systemctl status firewalld
   sed -i s/^SELINUX=.*$/SELINUX=permissive/ /etc/selinux/config
   setenforce 0
   yum update -y
   yum install -y https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.6-3.3.el7.x86_64.rpm
   sudo curl  https://download.docker.com/linux/centos/docker-ce.repo -o /etc/yum.repos.d/docker-ce.repo
   sudo yum makecache
   sudo dnf -y install docker-ce
   sudo dnf -y install  git
   sudo systemctl enable --now docker
   sudo curl -L https://github.com/docker/compose/releases/download/1.25.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
   sudo chmod +x /usr/local/bin/docker-compose && ln -sv /usr/local/bin/docker-compose /usr/bin/docker-compose
   sudo docker-compose --version
   sudo docker --version
  ```

Run [Kong API Gateway, Community Edition](https://konghq.com/kong-community-edition)
development setup with [docker-compose](https://docs.docker.com/compose).
Includes [Konga](https://github.com/pantsel/konga) as admin webapp.

Originally based on [Yuan Cheung's docker-compose-kong](https://github.com/zhangyuan/docker-compose-kong), with the following additions:

- Add [declarative configuration examples](https://github.com/asyrjasalo/kongpose/tree/master/examples)
  - Includes securing [Kong Admin API](https://docs.konghq.com/0.14.x/secure-admin-api/#kong-api-loopback)
- Use PostgreSQL 9.6 over 9.5, and Alpine Linux based image for smaller size
- Prefer `kong-migration` for initializing the database, rather than `setup.sh`
- Prefer Docker's own health checks, over using `wait-for-it.sh`
- Remove `bash` from built images, as it is not then needed
- Tidy up `docker-compose.yml`, removed `links` as they are not mandatory here
- Remove `start.sh` as `docker-compose restart` is a single command anyway
- Add MongoDB for storing Konga users
- Improve healthchecks for checking if database migrations have ran
- Upgrade Kong to 1.0.0rc3, use Alpine Linux based image for smaller size
- Upgrade Kong Dashboard to latest, to support Kong >= 0.13
  - though commented out in `docker-compose.yml` as Konga is enough


## Usage

 ```bash
    docker-compose up
  ```
  
## Swarm setup

 ```bash
docker stack deploy --compose-file=docker-compose-swarm.yaml kong
  ```


## Endpoints

### Kong

- Proxy: [http://localhost:8000](http://localhost:8000)
- Proxy w/ SSL: [https://localhost:8443](https://localhost:8443)
- Admin API: [http://localhost:8001](http://localhost:8001)

Kong uses PostgreSQL (9.6) with a persistent Docker volume for its credentials.

### Konga

- GUI: [http://localhost:1337](http://localhost:1337)

The following default users are configured in `konga/user_seed.js`:
- admin / adminadminadmin
- demo / demodemodemo

After logging in as admin, create a new connection with URL `http://kong:8001`.

Konga uses MongoDB (4.1) with a persistent Docker volume for its credentials.
