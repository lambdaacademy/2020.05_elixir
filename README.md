# 2020.05_elixir
School of Elixir 2020

## Contact

Rafał Słota: <rafal.slota@gmail.com>

## Useful links

* “Pointing Poker” - https://www.pointingpoker.com/
* Elixir - https://elixir-lang.org/install.html
* Phoenix - https://hexdocs.pm/phoenix/installation.html
* LiveView - https://hexdocs.pm/phoenix_live_view/installation.html
* Docker - https://docs.docker.com/get-docker/
* Bootstrap - https://getbootstrap.com/docs/4.0/components


## 1. Environment

### 1.1. Hex

```elixir
mix local.hex
```

### 1.2. Phoenix

```elixir
mix archive.install hex phx_new 1.5.1
```

## 2. Project preparation

### 2.1. Project init && dependencies

```elixir
mix phx.new pointing_poker --live
cd pointing_poker
npm install bootstrap jquery popper.js --prefix assets
npm install sass-loader node-sass webpack --prefix assets

npm install --prefix assets
```

### 2.2. Database setup

Remove `PointingPoker.Repo` child from `PointingPoker.Application`

### 2.3. Enabling Bootstrap

In `assets/js/app.js` add:

```
import "bootstrap"
```

In `assets/css/app.scss` replace:

```
@import "./phoenix.css";
```

with:

```
@import "~bootstrap/scss/bootstrap";
```

### 2.4. Starting project

```elixir
mix phx.server
```

Built in Dashboard: http://localhost:4000/dashboard


## 3. Project deployment

### 3.1. Setting up the release

We can prepare the release by:

```elixir
# Generate release boot scripts
mix release.init

# Initial setup
mix deps.get --only prod
MIX_ENV=prod mix compile

# Install / update  JavaScript dependencies
npm install --prefix ./assets

# Compile assets
npm run deploy --prefix ./assets
mix phx.digest
```

Next, we need to create `config/releases.exs` file as primary release configuration file.

### 3.2. Running a release

After we do that, we can generate the release with:

```elixir
MIX_ENV=prod mix release
```

We can run it with:

```elixir
export SECRET_KEY_BASE=`mix phx.gen.secret`
_build/prod/rel/pointing_poker/bin/pointing_poker start
```

### 3.3. Building docker image

Place `Dockerfile` that you can find next to this `README.md` in you project's root.

```elixir
docker build . -t pointing_poker:latest
```

We can try to run the container by:

```elixir
docker run -e SECRET_KEY_BASE=`mix phx.gen.secret` -p 8080:4000 -it pointing_poker:latest
```

### 3.4. docker-compose.yml

### 3.5. Using distributed registry

Add the following to project dependencies:

```elixir
{:syn, "~> 2.1"}
```

Next, we need to replace `Registry` with `:syn`.

### 3.6. Adding auto-discovery

Add the following to project dependencies:

```elixir
{:libcluster, "~> 3.2"}
```

Add to Application supervisor:

```elixir
{Cluster.Supervisor, [cluster_config(), [name: PointingPoker.ClusterSupervisor]]}


# With the following function definition:
defp cluster_config() do
    [
      gossip: [
        strategy: Cluster.Strategy.Gossip,
        config: [
          port: 45892,
          if_addr: "0.0.0.0",
          multicast_addr: "230.1.1.251",
          multicast_ttl: 1
        ]
      ]
    ]
  end
```

### 3.7. Configure ERTS to use deterministic ports

In `rel/vm.args.eex`:

```erlang
-kernel inet_dist_listen_min 4370
-kernel inet_dist_listen_max 4370
```

In `rel/env.sh/eex`:

```bash
export RELEASE_DISTRIBUTION=name
export RELEASE_NODE=<%= @release.name %>@`hostname -i`
```

