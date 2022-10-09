# Secure CockroachDB Cluster, inspired by [Docker tutorial](https://docs.docker.com/compose/django/)
Simple 3 node *secure* CockroachDB cluster with HAProxy acting as load balancer

* UPDATED: 11/11/20

Prerequisites:

## Services
* `roach-0` - CockroachDB node
* `roach-1` - CockroachDB node
* `roach-2` - CockroachDB node
* `lb` - HAProxy acting as load balancer
* `roach-cert` - Holds certificates as volume mounts

## Getting started
>If you are using Google Chrome as your browser, you may want to navigate here `chrome://flags/#allow-insecure-localhost` and set this flag to `Enabled`.

1) because operation order is important, execute `./up.sh` instead of `docker compose up`
   - monitor the status of services via `docker-compose logs`
2) `docker compose ps`
3) visit the CockroachDB [Admin UI](https://localhost:8080) and login with username `roach` and password `roach`
4) visit the [HAProxy UI](http://localhost:8081)

### Open Interactive Shells
```bash
docker exec -ti roach-0 /bin/bash
docker exec -ti roach-1 /bin/bash
docker exec -ti roach-2 /bin/bash
docker exec -ti lb /bin/sh

# shell
docker exec -ti roach-cert /bin/sh

# cli inside the container
cockroach sql --certs-dir=/certs --host=lb

# directly
docker exec -ti roach-0 cockroach sql --certs-dir=/certs --host=lb
```

access [HAProxy](http://localhost:8081)
