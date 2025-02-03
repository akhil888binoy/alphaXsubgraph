Running your own Graph Node and deploying a subgraph for a custom blockchain like Bitlayer Testnet involves several steps. Below is a detailed step-by-step guide to help you set this up on your remote server.

---

### **Step 1: Set Up an Ethereum Node**
Since Bitlayer Testnet is a custom blockchain, you need to run a node that supports it. However, Bitlayer Testnet is not natively supported by Ethereum clients like Geth or Erigon. Instead, you need to use the provided RPC endpoint (`https://rpc.ankr.com/bitlayer_testnet`) to interact with the blockchain.

You don't need to run a full node for Bitlayer Testnet because you can use the provided RPC endpoint. Skip this step and proceed to the next one.

---

### **Step 2: Set Up PostgreSQL and IPFS**
You need to install and configure PostgreSQL and IPFS on your server.

#### **Install PostgreSQL**
1. **Update your server:**
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Install PostgreSQL:**
   ```bash
   sudo apt install postgresql postgresql-contrib -y
   ```

3. **Start and enable PostgreSQL:**
   ```bash
   sudo systemctl start postgresql
   sudo systemctl enable postgresql
   ```

4. **Create a PostgreSQL user and database for Graph Node:**
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

#### **Install IPFS**
1. **Download and install IPFS:**
   ```bash
   wget https://dist.ipfs.tech/kubo/v0.18.1/kubo_v0.18.1_linux-amd64.tar.gz
   tar -xvzf kubo_v0.18.1_linux-amd64.tar.gz
   cd kubo
   sudo bash install.sh
   ```

2. **Initialize IPFS:**
   ```bash
   ipfs init
   ```

3. **Start IPFS:**
   ```bash
   ipfs daemon
   ```

---

### **Step 3: Deploy Graph Node**
Now, you need to clone and configure the Graph Node repository.

#### **Install Dependencies**
1. **Install Rust (required for Graph Node):**
   ```bash
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   source $HOME/.cargo/env
   ```

2. **Install other dependencies:**
   ```bash
   sudo apt install build-essential clang libssl-dev pkg-config -y
   ```

#### **Clone and Configure Graph Node**
1. **Clone the Graph Node repository:**
   ```bash
   git clone https://github.com/graphprotocol/graph-node.git
   cd graph-node
   ```

2. **Create a `.env` file for configuration:**
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

3. **Build and run Graph Node:**
   ```bash
   cargo build
   cargo run -p graph-node --release -- \
     --postgres-url postgresql://graphuser:yourpassword@localhost:5432/graphnode \
     --ethereum-rpc bitlayer:https://rpc.ankr.com/bitlayer_testnet \
     --ipfs 127.0.0.1:5001
   ```

---

### **Step 4: Create and Deploy Your Subgraph**
Now that your Graph Node is running, you can create and deploy a subgraph.

#### **Install Graph CLI**
1. **Install Node.js and npm:**
   ```bash
   sudo apt install nodejs npm -y
   ```

2. **Install Graph CLI:**
   ```bash
   npm install -g @graphprotocol/graph-cli
   ```

#### **Create a Subgraph**
1. **Initialize a new subgraph:**
   ```bash
   graph init --from-contract <CONTRACT_ADDRESS> --network bitlayer --index-events
   ```
   Replace `<CONTRACT_ADDRESS>` with your deployed contract address on Bitlayer Testnet.
   Select any available network 

2. **Navigate to your subgraph directory:**
   ```bash
   cd your-subgraph-name
   ```

3. **Replace values in subgraph.yaml*
   ```bash
    network: bitlayer
   ```
   Change network value to bitlayer .

4. **Create the subgraph**
   ```bash
   graph create --node http://localhost:8020/ <SUBGRAPH_NAME>
   ```
   Replace `<SUBGRAPH_NAME>` with a your actual subgraph name.

5. **Deploy the subgraph:**
   ```bash
   graph deploy --node http://localhost:8020 --ipfs http://localhost:5001 <SUBGRAPH_NAME>
   ```

---

### **Step 5: Verify and Test**
1. **Check the Graph Node logs** to ensure your subgraph is syncing.
2. **Query your subgraph** using the GraphQL endpoint provided by your Graph Node.

---

This setup should work for Bitlayer Testnet. Documented by [Akhil Binoy](https://github.com/akhil888binoy)