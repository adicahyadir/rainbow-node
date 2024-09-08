## 🌈 Rainbow Node Installation Tutorial

### **System Requirements**

Ensure your system meets the following minimum requirements to run a Rainbow Node efficiently:

- **CPU**: Multi-core processor
- **RAM**: 4 GB RAM
- **Storage**: 50 GB
- **Internet**: Stable connection

### **1. Install Docker on Your VPS**

Docker is essential for running the Rainbow Node. Here are the steps to install Docker on your Linux VPS:

1. **Update your package list:**
   ```bash
   sudo apt-get update
   ```

2. **Install necessary packages for Docker:**
   ```bash
   sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
   ```

3. **Add Docker’s official GPG key:**
   ```bash
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   ```

4. **Add Docker repository:**
   ```bash
   sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
   ```

5. **Install Docker and start the service:**
   ```bash
   sudo apt-get update
   sudo apt-get install docker-ce docker-ce-cli containerd.io
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

### **2. Set Up Your Environment**

1. **Install `screen` to keep your session alive:**
   ```bash
   sudo apt-get install screen
   ```

2. **Configure the firewall to allow SSH connections and enable it:**
   ```bash
   sudo ufw allow ssh
   sudo ufw enable
   ```

3. **Start a screen session to run the node in the background:**
   ```bash
   screen -S rainbow
   ```

### **3. Create Directories and Clone Repositories**

1. **Create a directory for the Rainbow Node setup:**
   ```bash
   mkdir -p rainbow
   cd rainbow
   ```

2. **Clone the Rainbow Protocol repository:**
   ```bash
   git clone https://github.com/rainbowprotocol-xyz/btc_testnet4
   cd btc_testnet4
   ```

### **4. Download Docker Compose**

Docker Compose is necessary to manage multiple containers. Install it using the following commands:

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

### **5. Run Docker Containers and Create a Wallet**

1. **Start the Docker containers:**
   ```bash
   docker-compose up -d
   ```

2. **Access the Bitcoin container shell:**
   ```bash
   docker exec -it bitcoind /bin/bash
   ```

3. **Create a new wallet:**
   ```bash
   bitcoin-cli -testnet4 -rpcuser=yourusername -rpcpassword=yourpassword -rpcport=5000 createwallet yournamewallet
   ```

4. **Exit the container:**
   ```bash
   exit
   ```

### **6. Set Up the Indexer**

The `rbo_indexer_testnet` is crucial for indexing operations on the Rainbow Protocol. You can find more about it on its [GitHub repository](https://github.com/rainbowprotocol-xyz/rbo_indexer_testnet). Follow these steps to set it up:

1. **Clone the indexer repository:**
   ```bash
   cd
   git clone https://github.com/rainbowprotocol-xyz/rbo_indexer_testnet && cd rbo_indexer_testnet
   ```

2. **Download and configure the worker:**
   ```bash
   wget https://github.com/rainbowprotocol-xyz/rbo_indexer_testnet/releases/download/v0.0.1-alpha/rbo_worker
   chmod +x rbo_worker
   ```

### **7. Edit `docker-compose.yml` File**

1. **Open `docker-compose.yml` for editing:**
   ```bash
   nano docker-compose.yml
   ```

2. **Modify the file content as follows:**

   ```yaml
   version: '3'
   services:
     bitcoind:
       image: mocacinno/btc_testnet4:bci_node
       privileged: true
       container_name: bitcoind
       volumes:
         - /root/project/run_btc_testnet4/data:/root/.bitcoin/
       command: ["bitcoind", "-testnet4", "-server","-txindex", "-rpcuser=demo", "-rpcpassword=demo", "-rpcallowip=0.0.0.0/0", "-rpcbind=0.0.0.0:5000"]
       ports:
         - "8333:8333"
         - "48332:48332"
         - "5000:5000"
   ```

3. **Save and close the file** by pressing `CTRL + X`, `Y`, and `Enter`.

### **8. Start the Indexer**

1. **Run the Docker containers:**
   ```bash
   docker-compose up -d
   ```

2. **If you have existing containers, stop and remove them first:**
   ```bash
   docker stop <container_id>
   docker rm <container_id>
   ```

3. **Run the worker with the necessary parameters:**
   ```bash
   ./rbo_worker worker --rpc http://127.0.0.1:5000 --password yourpass --username yourusername --start_height 42000
   ```

### **9. Configure and Submit Principal ID**

1. **Edit the `principal.json` file:**
   ```bash
   nano ~/rbo_indexer_testnet/identity/principal.json
   ```

2. **Submit your Principal ID on the Rainbow Protocol testnet website:**
   - [Rainbow Protocol Explorer](https://testnet.rainbowprotocol.xyz/explorer)

### **10. Backup Your Wallet**

1. **Backup your wallet's private key:**
   ```bash
   nano ~/rbo_indexer_testnet/identity/private_key.pem
   ```

### **Additional Resources**

- **Source:** [Twitter Update](https://x.com/rbo_protocol/status/1829777687671669202)
- **Documentation:** [Rainbow Protocol Docs](https://docs.rainbowprotocol.xyz/)
- **GitHub Repository:** [Rainbow Indexer Testnet](https://github.com/rainbowprotocol-xyz/rbo_indexer_testnet)
