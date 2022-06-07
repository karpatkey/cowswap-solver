# cowswap-solver

## Commands:

1: ```docker-compose up --build -d```

2: ```docker-compose logs -f```

Note: You need to configure orderbook, solver and driver!


Commands:

Only if you don't want to use this docker-compose.yml and run all containers separately! 


------------------DB Migration--------------

```
git clone git@github.com:cowprotocol/services.git

docker run --name postgres -d -e POSTGRES_HOST_AUTH_METHOD=trust -e POSTGRES_USER=`whoami` -p 5432:5432 docker.io/postgres

docker build --tag services-migration -f docker/Dockerfile.migration .
# If you are running postgres in locally, your URL is `localhost` instead of `host.docker.internal`
docker run --add-host=host.docker.internal:host-gateway -ti -e FLYWAY_URL="jdbc:postgresql://host.docker.internal/?user="$USER"&password=" -v $PWD/database/sql:/flyway/sql services-migration migrate

```

-------------------run orderbook------------------

```
  docker run --name orderbook --network host -it -d ghcr.io/cowprotocol/services orderbook -- --db-url postgresql://dk:""@localhost:5432/dk  \
  --skip-trace-api true \
  --base-tokens 0xDDAfbb505ad214D7b80b1f830fcCc89B60fb7A83 \
  --node-url "https://rpc.xdaichain.com"

```

---------------------run solver----------------------

```
git clone git@github.com:gnosis/cow-dex-solver.git

docker build -t cowdexsolver -f docker/Dockerfile.binary .

docker run --name solver -d --network host -p8000:8000 -ti cowdexsolver cowdexsolver --bind-address 0.0.0.0:8000

```

-------------------run driver------------------------

```
docker run --name driver -it -d --network host ghcr.io/cowprotocol/services solver -- \
--orderbook-url http://0.0.0.0:8080 \
--base-tokens 0xDDAfbb505ad214D7b80b1f830fcCc89B60fb7A83 \
--node-url "https://rpc.xdaichain.com" \
--cow-dex-ag-solver-url "http://0.0.0.0:8000" \
--solver-account 0x7942a2b3540d1ec40b2740896f87aecb2a588731 \
--solvers CowDexAg \
--transaction-strategy DryRun
```


-----------------------run cowsap-----------------------

```
docker run -d --name cowswap --network host -p 80:80 dharmendrakariya/cow:v3
```


----------------------------NOTE-------------------------

Please chack the docker-compose and comments! basically this stack has cowswap integrated! if you want just change the orderbook-url with the staging url! or may be production url! 

```ex: --orderbook-url https://barn.api.cow.fi/xdai/api ```
