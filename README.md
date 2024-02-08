# aethos-operator
This repo comprises of binaries/packages to run the Aethos operator. There's 2 main ways for running the operator - via `docker` or `binary`

## Configuration
There's multiple ways of passing config to the operator - via `cli` or `config.yaml` file. If you pass both, CLI will overwrite the config from file.
For docker container, it's preferred to pass via cli. For use with binary, it's preferred to pass via config.yaml.

Here's the configuration arguments that can be passed to the operator:

```sh
GLOBAL OPTIONS:
   --config FILE                                        Load configuration from FILE
   --ecdsa-private-key value                            Ethereum private key [$ECDSA_PRIVATE_KEY]
   --aggregator-server-ip-port-address value            Aggregator server IP:PORT address [$AGGREGATOR_SERVER_IP_PORT_ADDRESS]
   --operator-id value                                  Operator ID [$OPERATOR_ID]
   --ecdsa-private-key-store-path value                 Ethereum private key store path [$ECDSA_PRIVATE_KEY_STORE_PATH]
   --environment value                                  Environment [$ENVIRONMENT]
   --node-task-server-host-and-port-to-broadcast value  Node task server host and port to receive broadcast messages [$NODE_TASK_SERVER_HOST_AND_PORT_TO_BROADCAST]
   --node-eigen-api-server-host-and-port value          Node eigen api server host and port [$NODE_EIGEN_API_SERVER_HOST_AND_PORT]
   --eigen-metrics-ip-port-address value                Eigen metrics ip port address [$EIGEN_METRICS_IP_PORT_ADDRESS]
   --enable-metrics                                     Enable metrics [$ENABLE_METRICS]
   --enable-node-api                                    Enable node api [$ENABLE_NODE_API]
   --help, -h                                           show help
```

## Set up with Docker

For docker, you would need to set up git credentials to pull from the `ghcr.io` container registry. You can refer to this [working with container registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry) article
Mainly, you need to follow the following steps -

 1. Get person access token (classic) from your acocunt. Make sure it has permissions to `packages` as referred to in article
 2. Save your personal access token (classic). Recommend saving your token as an environment variable `export CR_PAT=YOUR_TOKEN`
 3. Login to container registry `echo $CR_PAT | docker login ghcr.io -u USERNAME --password-stdin`
 4. Once logged in, you can pull the image `docker pull ghcr.io/aethosnetwork/operator:latest`
 5. You can run help command like following - `docker run ghcr.io/aethosnetwork/operator:latest --help`
    
Now, you can run the operator by using following command:

```sh
docker run ghcr.io/aethosnetwork/operator:latest --config /app/config.yaml --ecdsa-private-key <private_key_starting_with_0x> --aggregator-server-ip-port-address 34.41.39.208:50051
```

## Set up with binary

In the `v0` folder, there's 2 available binaries - for `darwin/arm64` and `linux/amd64` architectures. You can use either or depending on the platform you are running on. Steps - 

1. You can run help command like following `./operator-v0.0.0-darwin-arm64 --help`

Now, you can update the `config.yaml` with your configuration variables that you have been provided and run

```sh
./operator-v0.0.0-darwin-arm64 --config ../config.yaml --ecdsa-private-key <private_key_starting_with_0x>
```
