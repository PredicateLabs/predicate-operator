# Aethos Mainnet Node Operation
This guide provides instructions on setting up and running the Aethos Operator using either Docker or a binary executable. Current version for mainnet is `0.2.5` and is marked as `v0.2.5-mainnet`

# Run an Aethos Operator

## Hardware Requirements
* **CPU:** 4 Cores
* **Memory:** 16 GB
* **Storage:** 100 GB SSD

## Prerequisites

* **Registered with Eigenlayer** Registration key registered with Eigenlayer (see [here](https://docs.eigenlayer.xyz/eigenlayer/operator-guides/operator-installation)).
* **Defined Aethos AVS Signer Key** ECDSA Signer Key used for signing tasks (see [here](https://docs.eigenlayer.xyz/eigenlayer/operator-guides/key-management/intro#eigenlayer)).
* **Aethos team enabled registration key:** Aethos is currently permissioned. Your registration key must be on the allowlist of the Aethos ServiceManger contract. Please reach out to us if this has not already been complete.
* **Ethereum node (full/archive):** You can point to your local instance or to an RPC provider.

## Ports 

### Allow incoming requests from following IP on these PORTS for Mainnet
* **`3.19.97.70:9010`:** exposing this port enables inbound tasks.
* **`3.19.97.70:9090`:** exposing this port enable metrics collection.

Aethos allows custom ports. To use your desired ports whitelist our IP address for the ports of your choice and configure your port to broadcast accordingly for both metrics and the task server. For metrics, let us know if you choose another port number besides 9090. 

## Configuration
The Aethos Operator supports configuration via command-line interface (CLI), or a config.yaml file which is included in this directory.

If both methods are used, CLI arguments will take precedence over configurations specified in the config.yaml file.

Preferences
* Docker: It's preferred to use CLI arguments.
* Binary: It's preferred to use the config.yaml file.

---
## Docker Setup
### Steps
1. **Authenticate with GitHub Container Registry:**
   * Setup a GitHub Personal Access Token (PAT) with the packages permissions (refer to [this article](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry) for more details)   **NOTE**: must be a classic token with all packages and workflow permissions enabled.
   * Export your PAT as an environment variable: `export CR_PAT=YOUR_TOKEN`.
   * Log in to the container registry: `echo $CR_PAT | docker login ghcr.io -u GITHUB_USERNAME --password-stdin`
2. **Pull the Docker Image:**
   * Execute: `docker pull ghcr.io/aethosnetwork/operator:latest`
3. **Register with Aethos AVS:**
   ```sh 
   export AETHOS_SIGNING_ADDRESS="<your_aethos_avs_signing_address>"
   export REGISTRATION_PRIVATE_KEY="<your_eigenlayer_registered_operator_private_key>"
   export ETH_RPC_URL="<your_eth_rpc_url>"
   export AVS_DIRECTORY_ADDRESS="0x135DDa560e946695d6f155dACaFC6f1F25C1F5AF"
   export AVS_SERVICE_MANAGER_ADDRESS="0x07E26BF8060e33fA3771d88128b75493750515c1"

   docker run ghcr.io/aethosnetwork/operator:latest register \
     --config=/app/config.yaml \
     --registration-private-key=${REGISTRATION_PRIVATE_KEY} \
     --avs-directory-address=${AVS_DIRECTORY_ADDRESS} \
     --aethos-signing-address=${AETHOS_SIGNING_ADDRESS} \
     --avs-service-manager-address=${AVS_SERVICE_MANAGER_ADDRESS} \
     --eth-rpc-url=${ETH_RPC_URL}
   ```

   * If you are passing in the Eigenlayer-registered operator's private key via keystore file, use the following command template to run the operator, replacing placeholders with actual values:
   ```sh
   export AETHOS_SIGNING_ADDRESS="<your_aethos_avs_signing_address>"
   export REGISTRATION_PRIVATE_KEY_STORE_PATH="/path/to/your/keystore/ecdsa_file.json"
   export REGISTRATION_PRIVATE_KEY_PASSWORD="<your_registration_key_keystore_password>"
   export ETH_RPC_URL="<your_eth_rpc_url>"
   export AVS_DIRECTORY_ADDRESS="0x135DDa560e946695d6f155dACaFC6f1F25C1F5AF"
   export AVS_SERVICE_MANAGER_ADDRESS="0x07E26BF8060e33fA3771d88128b75493750515c1"


   docker run --network host -v "${REGISTRATION_PRIVATE_KEY_STORE_PATH}:/app/operatorkey.json" \
     ghcr.io/aethosnetwork/operator:latest register \
     --config=/app/config.yaml \
     --registration-private-key-store-path /app/operatorkey.json \
     --registration-private-key-password ${REGISTRATION_PRIVATE_KEY_PASSWORD} \
     --avs-directory-address=${AVS_DIRECTORY_ADDRESS} \
     --aethos-signing-address=${AETHOS_SIGNING_ADDRESS} \
     --avs-service-manager-address=${AVS_SERVICE_MANAGER_ADDRESS} \
     --eth-rpc-url=${ETH_RPC_URL}
   ```
4. **Run the Operator:**
   * If you are passing in the Eigenlayer-registered operator's private key via CLI, use the following command template to run the operator, replacing placeholders with actual values:
   ```sh 
   export AETHOS_SIGNING_PRIVATE_KEY="<your_private_key>"
   export NODE_TASK_SERVER_HOST_AND_PORT_TO_BROADCAST="<your_operator_ip_addr_and_tasks_port>"
   export ETH_RPC_URL="<your_eth_rpc_url>"
   export AGGREGATOR_SERVER_IP_PORT_ADDRESS="holesky.task.aethos.network:50051"
   export AVS_SERVICE_MANAGER_ADDRESS="0x07E26BF8060e33fA3771d88128b75493750515c1"
   export DB_PATH="<path_to_mounted_volume>"

   docker run --network host -v "${DB_PATH}:/app/data" \
     ghcr.io/aethosnetwork/operator:latest start \
     --aethos-signing-private-key ${AETHOS_SIGNING_PRIVATE_KEY} \
     --aggregator-server-ip-port-address ${AGGREGATOR_SERVER_IP_PORT_ADDRESS} \
     --node-task-server-host-and-port-to-broadcast ${NODE_TASK_SERVER_HOST_AND_PORT_TO_BROADCAST} \
     --avs-service-manager-address=${AVS_SERVICE_MANAGER_ADDRESS} \
     --eth-rpc-url=${ETH_RPC_URL} \
     --config /app/config.yaml
    ```
   Note: If you're using Windows, adapt these commands for the Command Prompt (`set` command) or PowerShell (`$env:` prefix).

   * If you are passing in the Eigenlayer-registered operator's private key via keystore file, use the following command template to run the operator, replacing placeholders with actual values:
   ```sh
   export AETHOS_SIGNING_PRIVATE_KEY_STORE_PATH="/path/to/your/keystore/ecdsa_file.json"
   export AETHOS_SIGNING_PRIVATE_KEY_PASSWORD="<your_signing_key_keystore_password>"
   export NODE_TASK_SERVER_HOST_AND_PORT_TO_BROADCAST="<your_operator_ip_addr_and_tasks_port>"
   export ETH_RPC_URL="<your_eth_rpc_url>"
   export AVS_SERVICE_MANAGER_ADDRESS="0x07E26BF8060e33fA3771d88128b75493750515c1"
   export AGGREGATOR_SERVER_IP_PORT_ADDRESS="task.aethos.network:50051"
   export DB_PATH="<path_to_mounted_volume>"

   docker run --network host \
     -v "${AETHOS_SIGNING_PRIVATE_KEY_STORE_PATH}:/app/operatorkey.json" \
     -v "${DB_PATH}:/app/data" \
     ghcr.io/aethosnetwork/operator:latest start \
     --aethos-signing-private-key-store-path /app/operatorkey.json \
     --aethos-signing-private-key-password ${AETHOS_SIGNING_PRIVATE_KEY_PASSWORD} \
     --aggregator-server-ip-port-address ${AGGREGATOR_SERVER_IP_PORT_ADDRESS} \
     --node-task-server-host-and-port-to-broadcast ${NODE_TASK_SERVER_HOST_AND_PORT_TO_BROADCAST} \
     --avs-service-manager-address=${AVS_SERVICE_MANAGER_ADDRESS} \
     --eth-rpc-url=${ETH_RPC_URL} \
     --config /app/config.yaml
   ```
   Note: If you're using Windows, adapt these commands for the Command Prompt (`set` command) or PowerShell (`$env:` prefix).

   * To view additional configuration options: `docker run ghcr.io/aethosnetwork/operator:latest --help`

---
## Binary Setup

## Steps

1. **Determine your system architecture**
   - Example: `darwin/arm64` for macOS on ARM, `linux/amd64` for Linux on AMD64.

2. **Navigate to the '../binaries' Directory**
   - This directory contains binaries for supported architectures.

3. **Review Help Documentation**
   - Execute the following command, replacing `<RELEASE_VERSION>` with the latest release version and `<ARCH>` with your actual architecture:
     ```sh
     ./operator-<RELEASE_VERSION>-<ARCH> --help
     ```

4. **Register with Aethos AVS**
   - Register by pasting private key string:
     ```sh
     export AETHOS_SIGNING_ADDRESS="aethos_avs_signing_address"
     export REGISTRATION_PRIVATE_KEY="eigenlayer_registered_operator_private_key"
     export ETH_RPC_URL="eth_rpc_url"
     export CONFIG_PATH="your_path_to_config_file"

     ./operator-<RELEASE_VERSION>-<ARCH> register \
       --config ${CONFIG_PATH} \
       --aethos-signing-address ${AETHOS_SIGNING_ADDRESS} \
       --registration-private-key ${REGISTRATION_PRIVATE_KEY} \
       --eth-rpc-url ${ETH_RPC_URL}
     ```
   - for keystore files, use config.yaml

5. **Run the Operator**
   - Update the `config.yaml` file with your provided configuration variables. Remember to update the `eth_rpc_url`, `node_task_server_host_and_port_to_broadcast`, and `db_path` in the `config.yaml`.

   - Start via signing key string:
     ```sh
     export AETHOS_SIGNING_PRIVATE_KEY="your_aethos_signing_private_key"
     export CONFIG_PATH="your_path_to_config_file"
     ./operator-<RELEASE_VERSION>-<ARCH> start --config ${CONFIG_PATH}--aethos-signing-private-key ${AETHOS_SIGNING_PRIVATE_KEY}
     ```
   - for keystore files, use config.yaml

Replace `ARCH`, `RELEASE_VERSION`, `CONFIG_PATH`, and `PRIVATE_KEY` with the appropriate values for your setup.
