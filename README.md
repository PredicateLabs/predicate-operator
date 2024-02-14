# For Aethos Operators
Welcome to the Aethos Operator documentation. This guide provides comprehensive instructions on setting up and running
the Aethos Operator using either Docker or a binary executable.

## Configuration
The Aethos Operator supports configuration via command-line interface (CLI) arguments or a config.yaml file.
If both methods are used, CLI arguments will take precedence over configurations specified in the config.yaml file.
### Configuration Preferences:
* Docker: It's preferred to use CLI arguments.
* Binary: It's preferred to use the config.yaml file.

### Configuration Options
Below are the available configuration options for the Aethos Operator:

```sh
GLOBAL OPTIONS:
 --config FILE                                        Load configuration from FILE
   --ecdsa-private-key value                            Ethereum private key for signing messages [$ECDSA_PRIVATE_KEY]
   --aggregator-server-ip-port-address value            Aggregator server IP:PORT address [$AGGREGATOR_SERVER_IP_PORT_ADDRESS]
   --operator-id value                                  Operator ID [$OPERATOR_ID]
   --ecdsa-private-key-store-path value                 Ethereum private key store path [$ECDSA_PRIVATE_KEY_STORE_PATH]
   --ecdsa-private-key-password value                   private key password for the key store file [$ECDSA_PRIVATE_KEY_PASSWORD]
   --environment value                                  Environment (development, production) [$ENVIRONMENT]
   --node-task-server-host-and-port-to-broadcast value  host and port to receive broadcast messages on [$NODE_TASK_SERVER_HOST_AND_PORT_TO_BROADCAST]
   --node-eigen-api-server-host-and-port value          host and port for eigen api server [$NODE_EIGEN_API_SERVER_HOST_AND_PORT]
   --eigen-metrics-ip-port-address value                host and port for metrics server [$EIGEN_METRICS_IP_PORT_ADDRESS]
   --enable-metrics                                     Enable metrics [$ENABLE_METRICS]
   --enable-node-api                                    Enable node api [$ENABLE_NODE_API]
   --help, -h                                           show help
```

## Docker Setup
Docker Setup

### Prerequisites
* Docker installed on your machine.
* A GitHub Personal Access Token (PAT) with packages permissions. Create a PAT here.

### Steps
1. Authenticate with GitHub Container Registry:
   * Setup a GitHub Personal Access Token (PAT) with packages permissions (refer to [this article](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry) for more details) .
   * Export your PAT as an environment variable: `export CR_PAT=YOUR_TOKEN`.
   * Log in to the container registry: `echo $CR_PAT | docker login ghcr.io -u GITHUB_USERNAME --password-stdin`
2. Pull the Docker Image:
   * Execute: `docker pull ghcr.io/aethosnetwork/operator:latest`

3. Run the Operator:
   * Use the following command template to run the operator, replacing placeholders with actual values:
   ```sh 
     docker run ghcr.io/aethosnetwork/operator:latest --ecdsa-private-key YOUR_PRIVATE_KEY --aggregator-server-ip-port-address 34.41.39.208:50051 --node-task-server-host-and-port-to-broadcast {PUBLIC_IP:9010} --operator-id ${YOUR_OPERATOR_ID}
    ```
   * To view additional configuration options: `docker run ghcr.io/aethosnetwork/operator:latest --help`


## Binary Setup

### Prerequisites
1. Determine your system architecture (darwin/arm64 for macOS on ARM, linux/amd64 for Linux on AMD64).

### Steps
1. Navigate to the 'v0' Directory:
   * This contains binaries for supported architectures.
2. Review Help Documentation:
   * Execute: ./operator-v0.0.0-YOUR_ARCH --help, replacing YOUR_ARCH with your actual architecture.
3. Run the Operator:
   * First, update the config.yaml with your provided configuration variables.
   * Then, execute the binary with your configuration:
    ```sh
    ./operator-v0.0.0-YOUR_ARCH --config YOUR_CONFIG_PATH/config.yaml --ecdsa-private-key YOUR_PRIVATE_KEY --node-task-server-host-and-port-to-broadcast {PUBLIC_IP:9010} --operator-id ${YOUR_OPERATOR_ID}
    ```
   * Replace YOUR_ARCH, YOUR_CONFIG_PATH, and YOUR_PRIVATE_KEY with the appropriate values for your setup.