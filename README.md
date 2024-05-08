# Welcome to Aethos
This is the Aethos Operator documentation - a guide which provides instructions on setting up and running the Aethos Operator using either Docker or a binary executable.

## We're live on Testnet
Aethos Testnet Phase Two is underway on Holesky 
- Service Manager Proxy [```0xdE93E0dA148e1919bb7f33cd8847F96e45791210```](https://holesky.etherscan.io/address/0xdE93E0dA148e1919bb7f33cd8847F96e45791210)
- Aggregator is running on ```holesky.task.aethos.network:50051```

## Prerequisites to Running an Operator
* **Must be registered with Eigenlayer on Holesky** Account registered with Eigenlayer as an operator (see [here](https://docs.eigenlayer.xyz/eigenlayer/operator-guides/operator-installation)).
* **Holesky node (full/archive):** You can point to your local instance or to an RPC provider.
* **Enabled operator:** Aethos testnet is permissioned. Your operator address must be on the allowlist on our ServiceManger contract. Please reach out to us if this has not already been complete.

## Hardware Requirements
* **16GB RAM** 
* **1TB Fast SSD**
* **4 cores CPU**

## Networking
* **Expose 9010 port for `18.118.236.196` :** exposing this port enables inbound tasks
* **Expose 9090 port for `18.118.236.196`:** exposing this port enable metrics collection
  
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
   --eth-rpc-url value                                  Ethereum RPC URL [$ETH_RPC_URL]
   --avs-service-manager-address value                  AVS Service Manager contract address [$AVS_SERVICE_MANAGER_ADDRESS]
   --help, -h                                           show help
```

## Docker Setup
### Steps
1. Authenticate with GitHub Container Registry:
   * Setup a GitHub Personal Access Token (PAT) with the packages permissions (refer to [this article](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry) for more details) **NOTE**: must be a classic token with all packages and workflow permissions enabled.
   * Export your PAT as an environment variable: `export CR_PAT=YOUR_TOKEN`.
   * Log in to the container registry: `echo $CR_PAT | docker login ghcr.io -u GITHUB_USERNAME --password-stdin`
2. Pull the Docker Image:
   * Execute: `docker pull ghcr.io/aethosnetwork/operator:latest`

3. Run the Operator:
      * If you are passing in the Eigenlayer-registered operator's private key via CLI, use the following command template to run the operator, replacing placeholders with actual values:
   ```sh 
      export PRIVATE_KEY="your_private_key"
      export NODE_TASK_SERVER_HOST_AND_PORT_TO_BROADCAST="your_operator_ip_addr_and_tasks_port"
      export ETH_RPC_URL="your_eth_rpc_url"
      export OPERATOR_ID="your_operator_id"
      export AGGREGATOR_SERVER_IP_PORT_ADDRESS="holesky.task.aethos.network:50051"
      export AVS_SERVICE_MANAGER_ADDRESS="0xdE93E0dA148e1919bb7f33cd8847F96e45791210"
      docker run --network host ghcr.io/aethosnetwork/operator:latest \
      --ecdsa-private-key ${PRIVATE_KEY} \
      --aggregator-server-ip-port-address ${AGGREGATOR_SERVER_IP_PORT_ADDRESS} \
      --node-task-server-host-and-port-to-broadcast ${NODE_TASK_SERVER_HOST_AND_PORT_TO_BROADCAST} \
      --avs-service-manager-address=${AVS_SERVICE_MANAGER_ADDRESS} \
      --eth-rpc-url=${ETH_RPC_URL} \
      --operator-id ${OPERATOR_ID} \
      --config /app/config.yaml

    ```
   Note: If you're using Windows, adapt these commands for the Command Prompt (`set` command) or PowerShell (`$env:` prefix).
      
      * If you are passing in the Eigenlayer-registered operator's private key via keystore file, use the following command template to run the operator, replacing placeholders with actual values:
   ```sh
      export ECDSA_KEYSTORE_FILE_ABSOLUTE_PATH="/path/to/your/keystore/file.json"
      export ECDSA_KEYSTORE_PASSWORD="your_ecsda_keystore_password"
      export NODE_TASK_SERVER_HOST_AND_PORT_TO_BROADCAST="your_operator_ip_addr_and_tasks_port"
      export ETH_RPC_URL="your_eth_rpc_url"
      export OPERATOR_ID="your_operator_id"
      export AVS_SERVICE_MANAGER_ADDRESS="0xdE93E0dA148e1919bb7f33cd8847F96e45791210"
      export AGGREGATOR_SERVER_IP_PORT_ADDRESS="holesky.task.aethos.network:50051"
      docker run --network host \
      -v "${ECDSA_KEYSTORE_FILE_ABSOLUTE_PATH}:/app/operatorkeys.json" \
      ghcr.io/aethosnetwork/operator:latest \
      --ecdsa-private-key-store-path /app/operatorkeys.json \
      --ecdsa-private-key-password ${ECDSA_KEYSTORE_PASSWORD} \
      --aggregator-server-ip-port-address ${AGGREGATOR_SERVER_IP_PORT_ADDRESS} \
      --node-task-server-host-and-port-to-broadcast ${NODE_TASK_SERVER_HOST_AND_PORT_TO_BROADCAST} \
      --avs-service-manager-address=${AVS_SERVICE_MANAGER_ADDRESS} \
      --eth-rpc-url=${ETH_RPC_URL} \
      --operator-id ${OPERATOR_ID} \
      --config /app/config.yaml
   ```
   Note: If you're using Windows, adapt these commands for the Command Prompt (`set` command) or PowerShell (`$env:` prefix).
   
   * To view additional configuration options: `docker run ghcr.io/aethosnetwork/operator:latest --help`


## Binary Setup

### Steps
1. Determine your system architecture (darwin/arm64 for macOS on ARM, linux/amd64 for Linux on AMD64).
2. Navigate to the 'v0' Directory:
   * This contains binaries for supported architectures.
3. Review Help Documentation:
   * Execute: `./operator-<RELEASE_VERSION>-<ARCH> --help`, replacing ARCH with your actual architecture and <RELEASE_VERSION> with latest release.
4. Run the Operator:
   * ```config.yaml``` has pre-set variables that are used for connecting to our aggregator
   * Update the config.yaml with your provided configuration variables. Remember to update the **eth_rpc_url** and **node_task_server_host_and_port_to_broadcast**.
   * Then, execute the binary with your configuration:
      ```sh
      export PRIVATE_KEY=your_private_key
      export OPERATOR_ID=your_operator_id
      export CONFIG_PATH=your_path_to_config_file
      ./operator-<RELEASE_VERSION>-<ARCH> --config ${CONFIG_PATH} --ecdsa-private-key ${PRIVATE_KEY} --operator-id ${OPERATOR_ID}
      ```
   * Replace ARCH, RELEASE_VERSION, CONFIG_PATH, and PRIVATE_KEY, and OPERATOR_ID with the appropriate values for your setup.
   
   
