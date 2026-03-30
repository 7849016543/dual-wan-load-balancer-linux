# dual-wan-load-balancer-linux
Dual WAN Load Balancer with Automatic Failover using Linux Policy Routing
# 🚀 Dual WAN Load Balancer with Auto Failover (Linux)

A production-ready Linux-based dual WAN load balancer using policy routing, systemd, and intelligent health checks.

## 📌 Features

- ✅ Load balancing across 2 ISPs (Equal weight)
- ✅ Automatic failover when one ISP goes down
- ✅ Automatic recovery when ISP comes back
- ✅ Slack notifications for outages
- ✅ Dynamic IP & gateway detection
- ✅ Policy-based routing (ip rule + routing tables)
- ✅ Real-time traffic monitoring
- ✅ systemd service for reliability

---

## 🏗️ Architecture
    Internet
    /     \
 ISP1     ISP2
  |         |
eno1      lan0
   \       /
    Linux Router
         |
     LAN Clients

     
---

## ⚙️ Technologies Used

- Linux Networking (iproute2)
- Policy Routing
- Bash scripting
- systemd
- DHCP + Routing Tables
- Slack Webhooks

---

## 🔁 How It Works

1. Script continuously monitors:
   - Gateway reachability
   - Internet connectivity (via ping to multiple hosts)

2. Based on status:
   - 🟢 Both up → Load balancing (multipath routing)
   - 🔴 One down → Switch to active ISP
   - ⚠️ Both down → Alert

3. Automatically updates:
   - Routing tables
   - Default routes
   - IP rules

---

## 📂 Components

### 1. dual-wan-monitor.sh
- Core logic
- Handles failover + load balancing

### 2. systemd Service
- Ensures auto-start and restart

### 3. Network Config
- Uses systemd-networkd
- Separate routing tables per ISP

### 4. traffic-monitor.sh
- Real-time bandwidth monitoring per interface

---

## 🚀 Setup Instructions

### 1. Copy scripts

sudo cp scripts/*.sh /usr/local/bin/
sudo chmod +x /usr/local/bin/*.sh

🔧 2. Setup systemd service

sudo cp systemd/dual-wan-monitor.service /etc/systemd/system/
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable dual-wan-monitor
sudo systemctl start dual-wan-monitor

**This ensures the WAN monitor runs automatically on boot and restarts on failure.
**

🌐 3. Configure network interfaces
sudo cp network-config/*.network /etc/systemd/network/
sudo systemctl restart systemd-networkd

This configures separate routing tables for each ISP using systemd-networkd.

⚙️ 4. Configure environment variables
sudo mkdir -p /etc/dual-wan
sudo nano /etc/dual-wan/.env

SLACK_WEBHOOK_URL=https://hooks.slack.com/services/XXXXX
WD_NUMBER=Router-01

Used for Slack alerts and identification.

▶️ 5. Verify everything is working
sudo systemctl status dual-wan-monitor
ip route show
ip rule show
You should see multipath routing when both ISPs are active.

📊 6. Run traffic monitor (optional)
sudo /usr/local/bin/traffic-monitor.sh
Displays real-time bandwidth usage per interface.

## 🚀 Setup Instructions

1. Copy scripts
2. Setup systemd
3. Configure network
4. Setup env
5. Verify
6. Monitor traffic

   ## 🧪 Testing Failover


sudo ip link set eno1 down
sudo ip link set eno1 up
