# Tempura Docker

This guide helps creating Docker Images and Containers for Tempura Testnet.

### Changes :factory:

* download of Tempura binaries instead of Peercoin ones
* donwload of ElectrumX fork where Tempura has been added as supported coin
* `RUN make` instead of `RUN make -j4` to avoid compiler killed for having too many threads
* docker-compose file configured to combine Tempura and ElectrumX

### Setup a Tempura node :whale:

1. Move into `tempura-{version}` folder and run `docker build -t tempura .` to compile a new **Docker Image** for tempura. 

2. Initialize and configure a **Docker Container** using:

```sh
docker run -p 9903:9903 -p 9904:9904 --name tempura-testnet -d tempura \
  -rpcallowip=0.0.0.0/0 \
  -rpcpassword=<PASSWORD> \
  -rpcuser=<USER> \
  -testnet=1 \
  -nominting
```

3. **Check** if you can reach the defult RPC port (9904 for testnet) running this command:

```sh
curl --user <USER>:<PASSWORD> --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "getblockchaininfo", "params": [] }'  -H 'content-type: text/plain;' localhost:9904/
```

and you should obtain something like this:

> {"result":{"chain":"main","blocks":457576,"headers":457576,"bestblockhash":"17a24a8073c8f6bc422fc4f6fe8c76da892d0693d0ad1aa499e4b9b2c047fe2b","difficulty":1710444103.933884,"mediantime":1571034759,"verificationprogress":0.9999997034325266,"initialblockdownload":false,"chainwork":"00000000000000000000000000000000000000000000000000336b3807456f56","size_on_disk":700956211,"pruned":false,"warnings":""},"error":null,"id":"curltest"}

4. Once the server is running, you can interact with the node using **peercoin-cli** from the host machine, since `-p` option opens port-forwarding for the guest operating system. Alternatively from the Container itself, opening Docker Desktop and clicking on the "CLI" button next to the corresponding Container.

5. To stop the node, giving it time to arrest correctly and avoiding a long startup next time, check all running Containers with `docker ps` and stop it with `docker stop -t 500 <PROCESS_ID>`.

### Setup a Tempura node with ElectrumX server :whale:

1. Move into `tempura-<VERSION>` folder and run `docker build -t tempura .` to compile a new **Docker Image** for Tempura. 

2. Move into `electrumx` folder and run `docker build -t electrumx .` to compile a new **Docker Image** for ElectrumX. 

3. Move into the main folder and run `docker-compose up -d` to combine the two images into a single Container.

Note that Tempura wallet requires valid domain SSL certificates to run properly and not the ones automatically generated by the server, which are self-signed.

To run ElectumX alone try: `docker run -v <PATH_TO_DIR>:/data -e DAEMON_URL=<USER>:<PASSWORD>@peercoind:9903 -e COIN=TempuraTestnet -e NET=testnet -p 50002:50002 --name testnet-tempura-server electrumx`

### Share Images across devices :computer:

You can manually export and share your Docker Images with `docker save tempura > my-image.tar` and you can load them on another machine using `docker load < my-image.tar`.

### References :books:

* Forked from: [peercoin/docker-peercoind](https://github.com/peercoin/docker-peercoind.git) and [peercoin/electrumx-docker](https://github.com/peercoin/electrumx-docker) 
* [Certbot](https://certbot.eff.org/docs/using.html?utm_source=pocket_mylist) to generate SSL certificates

Irrilevanti?
* [How to create a Docker Image](https://www.linux.com/training-tutorials/how-create-docker-image/?utm_source=pocket_mylist)
* [How to share Docker Images with others](https://www.cloudsavvyit.com/12326/how-to-share-docker-images-with-others/?utm_source=pocket_mylist)

