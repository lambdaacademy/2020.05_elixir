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
