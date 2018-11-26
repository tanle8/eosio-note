# Start your Node & Setup

If you don't already have docker installed, you can download it here: https://www.docker.com/community-edition

## Step 1: Get the docker image

The below statement will download an Ubuntu image which contains the compiled software.

```bash
docker pull eosio/eos:v1.4.2
```

## Step 2: Boot Node and Wallet

- In the last step you created a `contracts` directory, obtained the absolute path using `pwd` command.
- Replace both occurrences of "CONTRACTS_DIR" in the command below with the absolute path to your `contracts` directory.

```bash
docker run --name eosio \
  --publish 7777:7777 \
  --publish 127.0.0.1:5555:5555 \
  --volume CONTRACTS_DIR:CONTRACTS_DIR \
  --detach \
  eosio/eos:v1.4.2 \
  /bin/bash -c \
  "keosd --http-server-address=0.0.0.0:5555 & exec nodeos -e -p eosio --plugin eosio::producer_plugin --plugin eosio::chain_api_plugin --plugin eosio::history_plugin --plugin eosio::history_api_plugin --plugin eosio::http_plugin -d /mnt/dev/data --config-dir /mnt/dev/config --http-server-address=0.0.0.0:7777 --access-control-allow-origin=* --contracts-console --http-validate-host=false --filter-on='*'"
```

Example: in my case:

```bash
docker run --name eosio --publish 7777:7777 --publish 127.0.0.1:5555:5555 --volume /home/liem/source/bc/eos_space/contracts:/home/liem/source/bc/eos_space/contracts --detach eosio/eos:v1.4.2 /bin/bash -c "keosd --http-server-address=0.0.0.0:5555 & exec nodeos -e -p eosio --plugin eosio::producer_plugin --plugin eosio::chain_api_plugin --plugin eosio::history_plugin --plugin eosio::history_api_plugin --plugin eosio::http_plugin -d /mnt/dev/data --config-dir /mnt/dev/config --http-server-address=0.0.0.0:7777 --access-control-allow-origin=* --contracts-console --http-validate-host=false --filter-on='*'"
```

NOTE: These settings accomplish the following:

- Forward port 7777 and 5555 to host machine
- Alias a work volume on your local drive to the docker container.
- Run the Nodeos startup in bash. This command loads all the basic plugins, set the server address, enable CORS and add some contract debugging.
- Enable CORS with no restrictions (*)

> In the above configuration, CORS is enabled for * __for development purposes only__, you should __never__ enable CORS for * on a node that is publicly accessible!

## Step 3: Check the installation

### Step 3.1: Check that Nodeos is Producing Blocks

Run the following command

```bash
docker logs --tail 10 eosio
```

You should see some output in the console that looks like this:

```bash
info  2018-11-23T10:29:10.501 thread-0  producer_plugin.cpp:1490      produce_block        ] Produced block 0000006ee34befc5... #110 @ 2018-11-23T10:29:10.500 signed by eosio [trxs: 0, lib: 109, confirmed: 0]
info  2018-11-23T10:29:11.000 thread-0  producer_plugin.cpp:1490      produce_block        ] Produced block 0000006f4c172c33... #111 @ 2018-11-23T10:29:11.000 signed by eosio [trxs: 0, lib: 110, confirmed: 0]
info  2018-11-23T10:29:11.502 thread-0  producer_plugin.cpp:1490      produce_block        ] Produced block 000000703f9692a2... #112 @ 2018-11-23T10:29:11.500 signed by eosio [trxs: 0, lib: 111, confirmed: 0]
info  2018-11-23T10:29:12.000 thread-0  producer_plugin.cpp:1490      produce_block        ] Produced block 0000007174e865fb... #113 @ 2018-11-23T10:29:12.000 signed by eosio [trxs: 0, lib: 112, confirmed: 0]
info  2018-11-23T10:29:12.500 thread-0  producer_plugin.cpp:1490      produce_block        ] Produced block 00000072cfef6191... #114 @ 2018-11-23T10:29:12.500 signed by eosio [trxs: 0, lib: 113, confirmed: 0]
info  2018-11-23T10:29:13.001 thread-0  producer_plugin.cpp:1490      produce_block        ] Produced block 00000073ceb9ab5b... #115 @ 2018-11-23T10:29:13.000 signed by eosio [trxs: 0, lib: 114, confirmed: 0]
info  2018-11-23T10:29:13.501 thread-0  producer_plugin.cpp:1490      produce_block        ] Produced block 00000074c7ec28ea... #116 @ 2018-11-23T10:29:13.500 signed by eosio [trxs: 0, lib: 115, confirmed: 0]
info  2018-11-23T10:29:14.001 thread-0  producer_plugin.cpp:1490      produce_block        ] Produced block 000000756083e4f4... #117 @ 2018-11-23T10:29:14.000 signed by eosio [trxs: 0, lib: 116, confirmed: 0]
info  2018-11-23T10:29:14.501 thread-0  producer_plugin.cpp:1490      produce_block        ] Produced block 00000076853b2b42... #118 @ 2018-11-23T10:29:14.500 signed by eosio [trxs: 0, lib: 117, confirmed: 0]
info  2018-11-23T10:29:15.001 thread-0  producer_plugin.cpp:1490      produce_block        ] Produced block 00000077f101f6d8... #119 @ 2018-11-23T10:29:15.000 signed by eosio [trxs: 0, lib: 118, confirmed: 0]
```

### Step 3.2: Check the Wallet

Open the shell

```bash
sudo docker exec -it eosio bash
```

Run the following command

```bash
cleos --wallet-url http://127.0.0.1:5555 wallet list
```

You should see a response

```bash
Wallets:
[]
```

Now exit the shell

```bash
exit
```

Now that `keosd` is running correctly, type exit and then press enter to leave the `keosd` shell. From this point forward, you won't need to enter the containers with bash, and you'll be executing commands from your local system (Linux or Mac)

### Step 3.3: Check Nodeos endpoints

This will check that the RPC API is working correctly, pick one.

1. Check the `get_info` endpoint provided by the `chain_api_plugin` in your browser: http://localhost:7777/v1/chain/get_info
2. Check the same thing, but in console on your host machine

```bash
curl http://localhost:7777/v1/chain/get_info
```

## Step 4: Aliasing Cleos

You don't want to enter into the Docker container's bash every time you need to interact with Nodeos or Keosd. A solution to this is to create an alias.

Execute the following in your terminal for a temporary alias, or add it to your `.bash_rc` file if on Linux, or `.profile` file in on Mac OS if you wish for your alias to persist _indefinitely_.

```bash
alias cleos='docker exec -it eosio /opt/eosio/bin/cleos --url http://127.0.0.1:7777 --wallet-url http://127.0.0.1:5555'
```

If you add the alias to your `.bash_rc`, you will need restart your bash session. You can use this [tutorial](https://www.hostingadvice.com/how-to/set-command-aliases-linuxubuntudebian/) on how to add the alias to your `.bash_rc`.

## Step 5: Take Note of Useful Docker Commands

Start/Stop Container

```bash
docker start eosio
docker stop eosio
```

Bash

```bash
docker exec -it eosio bash
```

Remove the EOSIO Container

```bash
docker rm eosio
```