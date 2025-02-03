
# Running Your Own Graph Node for Bitlayer Testnet

This guide provides step-by-step instructions to set up and run your own Graph Node for the Bitlayer Testnet blockchain. It covers setting up PostgreSQL, IPFS, Graph Node, and deploying a subgraph.

---

## Prerequisites

- A remote server with Ubuntu (or any Linux-based OS).
- Basic knowledge of terminal commands.
- A deployed contract on Bitlayer Testnet.

---

## Step 1: Set Up PostgreSQL and IPFS

### Install PostgreSQL

1. Update your server:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. Install PostgreSQL:
   ```bash
   sudo apt install postgresql postgresql-contrib -y
   ```

3. Start and enable PostgreSQL:
   ```bash
   sudo systemctl start postgresql
   sudo systemctl enable postgresql
   ```

4. Create a PostgreSQL user and database for Graph Node:
   ```bash
   sudo -u postgres psql
   ```
   Inside the PostgreSQL shell:
   ```sql
   CREATE USER graphuser WITH PASSWORD 'yourpassword';
   CREATE DATABASE graphnode;
   GRANT ALL PRIVILEGES ON DATABASE graphnode TO graphuser;
   \q
   ```

### Install IPFS

1. Download and install IPFS:
   ```bash
   wget https://dist.ipfs.tech/kubo/v0.18.1/kubo_v0.18.1_linux-amd64.tar.gz
   tar -xvzf kubo_v0.18.1_linux-amd64.tar.gz
   cd kubo
   sudo bash install.sh
   ```

2. Initialize IPFS:
   ```bash
   ipfs init
   ```

3. Start IPFS:
   ```bash
   ipfs daemon
   ```

---

## Step 2: Deploy Graph Node

### Install Dependencies

1. Install Rust (required for Graph Node):
   ```bash
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   source $HOME/.cargo/env
   ```

2. Install other dependencies:
   ```bash
   sudo apt install build-essential clang libssl-dev pkg-config -y
   ```

### Clone and Configure Graph Node

1. Clone the Graph Node repository:
   ```bash
   git clone https://github.com/graphprotocol/graph-node.git
   cd graph-node
   ```

2. Create a `.env` file for configuration:
   ```bash
   nano .env
   ```
   Add the following content:
   ```env
   postgres_host=localhost
   postgres_user=graphuser
   postgres_pass=yourpassword
   postgres_db=graphnode
   ipfs=127.0.0.1:5001
   ethereum=bitlayer:https://rpc.ankr.com/bitlayer_testnet
   ```

3. Build and run Graph Node:
   ```bash
   cargo build
   cargo run -p graph-node --release -- \
     --postgres-url postgresql://graphuser:yourpassword@localhost:5432/graphnode \
     --ethereum-rpc bitlayer:https://rpc.ankr.com/bitlayer_testnet \
     --ipfs 127.0.0.1:5001
   ```

---

## Step 3: Create and Deploy Your Subgraph

### Install Graph CLI

1. Install Node.js and npm:
   ```bash
   sudo apt install nodejs npm -y
   ```

2. Install Graph CLI:
   ```bash
   npm install -g @graphprotocol/graph-cli
   ```

### Create a Subgraph

1. Initialize a new subgraph:
   ```bash
   graph init --from-contract <CONTRACT_ADDRESS> --network mainnet --index-events
   ```
   Replace `<CONTRACT_ADDRESS>` with your deployed contract address on Bitlayer Testnet. Select any available network during initialization.

2. Navigate to your subgraph directory:
   ```bash
   cd your-subgraph-name
   ```

3. Update the `subgraph.yaml` file:
   - Change the `network` field to `bitlayer`.
   - Ensure the `address` field contains your contract address.

4. Create the subgraph:
   ```bash
   graph create --node http://localhost:8020/ <SUBGRAPH_NAME>
   ```
   Replace `<SUBGRAPH_NAME>` with your actual subgraph name.

5. Deploy the subgraph:
   ```bash
   graph deploy --node http://localhost:8020 --ipfs http://localhost:5001 <SUBGRAPH_NAME>
   ```

---

## Step 4: Verify and Test

1. Check the Graph Node logs to ensure your subgraph is syncing.
2. Query your subgraph using the GraphQL endpoint provided by your Graph Node.

---

## Troubleshooting

- **PostgreSQL Authentication Issues**: If you encounter `Peer authentication failed`, refer to the [PostgreSQL Authentication Guide](#postgresql-authentication).
- **Graph Node Logs**: Check the logs for detailed error messages.

---

## Contributors

- [Akhil Binoy](https://github.com/akhil888binoy)

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

This README provides a comprehensive guide to setting up and running a Graph Node for Bitlayer Testnet. If you encounter any issues, feel free to open an issue or contribute to the repository!