# Welcome to Aethos
This is the Aethos Operator documentation - a guide which provides instructions on setting up and running the Aethos Operator using either Docker or a binary executable.

## Aethos Testnet
Aethos testnet phase three is live on Holesky!
- Service Manager Proxy [```0xdE93E0dA148e1919bb7f33cd8847F96e45791210```](https://holesky.etherscan.io/address/0xdE93E0dA148e1919bb7f33cd8847F96e45791210)
- Aggregator service is running on ```holesky.task.aethos.network:50051```

## Run an Aethos Operator

### Hardware Requirements
* **CPU:** 4 Cores
* **Memory:** 16 GB
* **Storage:** 100 GB SSD

### Prerequisites
* **Registered with Eigenlayer on Holesky** Registration key registered with Eigenlayer (see [here](https://docs.eigenlayer.xyz/eigenlayer/operator-guides/operator-installation)).
* **Defined Aethos AVS Signer Key** Signer Key used for signing tasks (see [here](https://docs.eigenlayer.xyz/eigenlayer/operator-guides/key-management/intro#eigenlayer)).
* **Aethos team enabled registration key:** Aethos testnet is permissioned. Your registration key must be on the allowlist of the Aethos ServiceManger contract. Please reach out to us if this has not already been complete.
* **Holesky node (full/archive):** You can point to your local instance or to an RPC provider.

### Expose the following IP/Ports
* **`18.118.236.196`:** exposing this port enables inbound tasks
* **`18.118.236.196`:** exposing this port enable metrics collection

### Register with Aethos
``` bash 
operator register --config=/app/config.yaml --alias-private-key=$ALIAS_PUBLIC_KEY --registration-private-key=$REGISTRATION_PRIVATE_KEY --eth-rpc-url=$ETH_RPC_URL
```
### Configuration
The Aethos Operator supports configuration via command-line interface (CLI) arguments or a config.yaml file.
If both methods are used, CLI arguments will take precedence over configurations specified in the config.yaml file.

Preferences
* Docker: It's preferred to use CLI arguments.
* Binary: It's preferred to use the config.yaml file.

---
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
      --config /app/config.yaml
   ```
   Note: If you're using Windows, adapt these commands for the Command Prompt (`set` command) or PowerShell (`$env:` prefix).

   * To view additional configuration options: `docker run ghcr.io/aethosnetwork/operator:latest --help`

---

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
      ./operator-<RELEASE_VERSION>-<ARCH> --config ${CONFIG_PATH} --ecdsa-private-key ${PRIVATE_KEY}
      ```
   * Replace ARCH, RELEASE_VERSION, CONFIG_PATH, and PRIVATE_KEY, and OPERATOR_ID with the appropriate values for your setup.
---