# Drosera-Network
![Alt Text](https://github.com/yonarebahan/drosera-tutorial-indo/blob/main/Untitled.png)

# Recommended System Requirements
* 2 CPU Cores
* 4 GB RAM
* 20 GB Disk Space

Official [Discord](https://discord.gg/UXAdpTYjgr)

# ✅Step 1 — Initial Setup (System Requirements & Dependencies)
### 1. Update your system and install the necessary dependencies:
```
sudo apt-get update && sudo apt-get upgrade -y
sudo apt install curl ufw iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```

# ✅Step 2 — Install Docker
```
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update -y && sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

# Test docker
sudo docker run hello-world
```

# ✅Step 3 — Install Required CLI Tools
### 1. Install Drosera CLI
```
curl -L https://app.drosera.io/install | bash
source ~/.bashrc
droseraup
```
### 2. Install Foundry CLI
```
curl -L https://foundry.paradigm.xyz | bash
source ~/.bashrc
foundryup
```
### 3. Install Bun
```
curl -fsSL https://bun.sh/install | bash
source ~/.bashrc
```

# ✅Step 4 — Deploy Trap
### 1. Clone & set up the project

* Replace "your_email@gmail.com" with your email
* Replace "your_username" with your GitHub username
```
mkdir my-drosera-trap
cd my-drosera-trap
git config --global user.email "email_kamu@example.com"
git config --global user.name "username_kamu"
forge init -t drosera-network/trap-foundry-template
```
### 2. Compile Trap
```
bun install
forge build
```
> Ignore warnings during compile.

### 3. Deploy Trap to testnet
```
DROSERA_PRIVATE_KEY=YOUR_EVM_PRIVATE_KEY drosera apply
```
* Replace `YOUR_EVM_PRIVATE_KEY` with your EVM wallet `privatekey` (Ensure it's funded with `Holesky ETH`)
* Enter the command, when prompted, write `ofc` and press Enter.

![image](https://github.com/user-attachments/assets/6d1161f1-4423-4ce6-a1a2-77ce567186dc)

---

### 4. Check Trap in Dashboard
1- Connect your Drosera EVM wallet: https://app.drosera.io/

2- Click on `Traps Owned` to see  your deployed Traps OR search your Trap address.

![image](https://github.com/user-attachments/assets/9c39eea0-0aaf-417d-8552-765ff33f8a5e)

# ✅Step 5 — Bloom Boost & Trap Test
* Go to: https://app.drosera.io/
* Log in → Wallet → Click "Traps Owned"
* Select your Trap → Click "Send Bloom Boost" and deposit some Holesky ETH

![image](https://github.com/user-attachments/assets/2f5216fd-fdf9-4732-96d0-959b3fbce479)

### Run dryrun test:
```
drosera dryrun
```

# ✅ Step 6 — Run as a Node Operator

### 1. Edit whitelist operator:
```
cd my-drosera-trap
nano drosera.toml
```
### Clear everything inside and replace with this:
```
ethereum_rpc = "https://ethereum-holesky-rpc.publicnode.com"
drosera_rpc = "https://seed-node.testnet.drosera.io"
eth_chain_id = 17000
drosera_address = "0xea08f7d533C2b9A62F40D5326214f39a8E3A32F8"

[traps]

[traps.mytrap]
path = "out/HelloWorldTrap.sol/HelloWorldTrap.json"
response_contract = "0xdA890040Af0533D98B9F5f8FE3537720ABf83B0C"
response_function = "helloworld(string)"
cooldown_period_blocks = 33
min_number_of_operators = 1
max_number_of_operators = 2
block_sample_size = 10
private = true
address = "YOUR_TRAP_ADDRESS"
private_trap = true
whitelist = ["YOUR_OPERATOR_ADDRESS"]

```
* Replace `Operator_Address` with your EVM wallet `Public Address` between " " symbols
* Your `Public Address` is your `Operator_Address`.
* Replace `YOUR_TRAP_ADDRESS` with your trap config address between " " symbols
* 
![image](https://github.com/user-attachments/assets/6d1161f1-4423-4ce6-a1a2-77ce567186dc)

**2- Update Trap Configuration:**
```bash
DROSERA_PRIVATE_KEY=YOUR_EVM_PRIVATE_KEY drosera apply
```
* Replace `YOUR_EVM_PRIVATE_KEY` with your EVM wallet `privatekey`

* Your Trap should be private now with your operator address whitelisted internally.

![image](https://github.com/user-attachments/assets/9ae6d58e-3be7-4d0d-9c4b-3b486224df4e)

# ✅Step 7 — Install & Register Operator
### 1. Download & install CLI Operator
```
cd ~
curl -LO https://github.com/drosera-network/releases/releases/download/v1.16.2/drosera-operator-v1.16.2-x86_64-unknown-linux-gnu.tar.gz
tar -xvf drosera-operator-v1.16.2-x86_64-unknown-linux-gnu.tar.gz
sudo cp drosera-operator /usr/bin
drosera-operator --version
```
### 2. Register Operator
```
drosera-operator register --eth-rpc-url https://ethereum-holesky-rpc.publicnode.com --eth-private-key PRIVATE_KEY_MU
```

# ✅Step 8 — Run as systemd service
### Enter this command in the terminal, But first replace:
* `PV_KEY` with your `privatekey`
* `VPS_IP` with your solid vps IP (without anything else)
* if using a `local` system, then replace vps ip with `0.0.0.0`
```
sudo tee /etc/systemd/system/drosera.service > /dev/null <<EOF
[Unit]
Description=drosera node service
After=network-online.target

[Service]
User=$USER
Restart=always
RestartSec=15
LimitNOFILE=65535
ExecStart=$(which drosera-operator) node --db-file-path $HOME/.drosera.db --network-p2p-port 31313 --server-port 31314 \
    --eth-rpc-url https://ethereum-holesky-rpc.publicnode.com \
    --eth-backup-rpc-url https://1rpc.io/holesky \
    --drosera-address 0xea08f7d533C2b9A62F40D5326214f39a8E3A32F8 \
    --eth-private-key PV_KEY \
    --listen-address 0.0.0.0 \
    --network-external-p2p-address VPS_IP \
    --disable-dnr-confirmation true

[Install]
WantedBy=multi-user.target
EOF
```

# ✅Step 9 — Open Ports + Start Service
Open firewall ports:
```
sudo ufw allow ssh
sudo ufw allow 22
sudo ufw allow 31313/tcp
sudo ufw allow 31314/tcp
sudo ufw enable
```
### Run Operator
```
# reload systemd
sudo systemctl daemon-reload
sudo systemctl enable drosera

# start systemd
sudo systemctl start drosera
```

###  Check Node Health
```
journalctl -u drosera.service -f
```

![image](https://github.com/user-attachments/assets/a4ad6e66-4749-4780-9347-c878399d4067)

> !! No problem if you are receiveing `WARN drosera_services::network::service: Failed to gossip message: InsufficientPeers`

### Optional commands
```
# Stop node
sudo systemctl stop drosera

# Restart node
sudo systemctl restart drosera
```
**Now running your node using `SystemD`, you can Jump to step 7.**
---

# ✅Step 10 — Monitor & Opt-in to Trap
In the dashboard., Click on `Opt in` to connect your operator to the Trap

![image](https://github.com/user-attachments/assets/5189b5cb-cb46-4d10-938a-33f71951dfc2)

---

## 8. Check Node Liveness
Your node will start producing greeen blocks in the dashboard

![image](https://github.com/user-attachments/assets/9ad08265-0ea4-49f7-85e5-316677245254)

### Done ✅

### Check my youtube for video tutorial : https://www.youtube.com/@Oxclone-Network



