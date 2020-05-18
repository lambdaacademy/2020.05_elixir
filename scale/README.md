# 2020.05_elixir
School of Elixir 2020


## 1. Big scale test

### 1.1. Setup

* Build the docker image as per instructions in point 3.3. of regular [instructions](../README.md).
* Download the modified [docker-compose.yml](docker-compose.yml)

### 1.2. Let's try it!

We can start the service by:

```bash
docker-compose up -d --scale pointing_poker=10
```

This will: bring up 10 instances of Pointing Poker (no port binding!) and set up Traefik HTTP reverse-proxy.
Admin interface of the proxy will be available @ `http://localhost:8080`, you can for example see all discovered Pointing Poker nodes @ `http://localhost:8080/dashboard/#/http/services/pointing_poker@docker` (please keep in mind that it will take a minute or so for this to work).
The Pointing Poker is available @ `http://localhost/` (yes, default port 80). The reverse proxy will do round-robin loadbalance between all instances of Pointing Poker on each request. This means, that we can just open few tabs with Pointing Poker and you can be almost sure that most of them are connected to different nodes. You can watch `docker-compose logs -f` to see which node responds to each request.

By re-running the command above with different `--scale pointing_poker=X` value, we can adjust number of nodes. Docker Compose will only add/remove containers that need to be added/removed without re-deploying everything, so this should not break the state of application. Traefik HTTP reverse-proxy should pickup the changes after few seconds (it monitors Docker's socket to list new containers all the time).

Please notice the `PORT=80` env variable set for `pointing_poker`! - now the API is available to *client* at `http://localhost:80`, while server is still binded to port `4000` in the container - Phoenix needs to know that to generate valid static links.

Please be mindful about the number of container that you run - Erlang Distributed creates a communication **mesh** so the communication load (at least initially) may quickly became too big depending on the host machine. For this reason, this kind of environment should normally be run as Kubernetes or Docker Stack (swarm - https://docs.docker.com/engine/reference/commandline/stack/) cluster on multiple nodes. In case you have access to more then one physical node, you can quickly adopt this `docker-compose.yml` by adding an "overlay" network and reconfiguring Traefik to [swarm mode](https://docs.traefik.io/providers/docker/#docker-swarm-mode).