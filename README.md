# 🌐 Net-Verify: IP Subnet Calculator & Linux Network Toolkit

Net-Verify is a hybrid Python/Linux CLI tool designed to bridge the gap between theoretical network math and practical system administration.

Unlike standard subnet calculators, this tool features a **"Live Mode"** that interacts directly with the Linux kernel (via `subprocess`) to auto-detect your physical network configuration and verify connectivity to the calculated Gateway.

## 🚀 Features

* **Core Algorithm:** Implements bitwise logic (AND/OR operations) to calculate Network ID, Broadcast, and Host Ranges from scratch (No external libraries used for math).
* **Visual Analysis:** Displays Binary Representations of IP addresses and Netmasks to demonstrate the underlying math to the user.
* **Linux Integration (Live Mode):**
    * Auto-detects active interface IP using `ip addr`.
    * Validates network calculations by performing a Ping Sweep of the Gateway and neighbors using `subprocess`.
* **Subnet Automation:** Dynamically splits large networks (e.g., `/24`) into smaller, valid subnets (e.g., `/27`).

## 📂 Project Structure

```text
PySubnet-Linux/
│
├── core_calc.py         # The "Brain": Handles binary math & subnet logic
├── linux_ops.py       # The "Hands": Wraps Linux commands (ip, ping)
├── main.py            # The "Face": CLI Interface & Argument Parsing
├── requirements.txt   # Dependencies (Standard Library only)
└── README.md          # Documentation
````

## 🛠 Installation & Setup

1.  **Clone the repository:**

    ```bash
    git clone https://github.com/devsharma1209/PySubnet-Linux.git
    cd PySubnet-Linux
    ```

2.  **Set executable permissions:**
    (Recommended for Linux/Mac/AWS EC2 users to ensure scripts run smoothly)

    ```bash
    chmod +x main.py
    ```

3.  **Run the help command to verify installation:**

    ```bash
    python3 main.py --help
    ```

## 💻 Usage Guide

### 1️⃣ Scenario A: The "Scientific" Calculation

Use this mode to demonstrate the core bitwise algorithms and binary visualization without needing a network connection.

**Command:**

```bash
python3 main.py --mode calc --target 192.168.10.50/24
```

**What it illustrates:**

  * Converts the IP and Mask to Binary (e.g., `11000000...`).
  * Calculates the Network Address (`.0`) and Broadcast Address (`.255`).
  * Determines the usable host range (`.1` to `.254`).

### 2️⃣ Scenario B: Live Linux Mode (Integration)

Use this on a Linux machine (VM, Ubuntu, or AWS EC2) to demonstrate system integration.

**Command:**

```bash
python3 main.py --mode live
```

**How it works:**

1.  **Auto-Discovery:** The script executes `ip -4 addr show` to find your actual private IP.
2.  **Calculation:** It applies the subnet mask to determine where your network starts and ends.
3.  **Verification:** It executes `ping` commands to check if the Gateway (Router) is reachable.

**Sample Output (AWS EC2):**

```text
[*] Mode: LIVE LINUX INTEGRATION
[+] Detected: 172.31.73.60/20

--- LIVE REACHABILITY CHECK ---
Target IP            | Status
-----------------------------------
172.31.64.0          | DOWN           <-- Network ID (Expected Down)
172.31.64.1          | UP (Reachable) <-- Gateway Router (Proof of Connection)
172.31.73.60         | UP (Reachable) <-- Self (Loopback Check)
```

### 3️⃣ Scenario C: Subnetting (Advanced)

Use this to demonstrate splitting a large network into smaller segments.

**Command:**

```bash
python3 main.py --target 10.0.0.1/24 --subnet 27
```

**Result:**
The tool slices the `/24` network (256 hosts) into multiple `/27` networks (32 hosts each) and prints the range for every slice.

## 🔄 How to Reproduce the Results
To replicate the exact outputs shown above:

### 1. On any Linux machine (Ubuntu, Kali, EC2, WSL):
```bash
git clone https://github.com/devsharma1209/PySubnet-Linux.git
cd PySubnet-Linux
python3 main.py --mode calc --target 192.168.10.50/24
```

You should see the same structure:
- Network ID  
- Broadcast  
- Binary Mask  
- Host Range  

### 2. For Live Mode (requires real Linux networking):
```bash
python3 main.py --mode live
```

You will get:
- Auto-detected interface IP  
- /CIDR  
- Gateway reachability (UP/DOWN)  
- Neighbor scan (ping)

### 3. For Subnetting:
```bash
python3 main.py --target 10.0.0.1/24 --subnet 27
```

This prints all sliced /27 subnets with ranges.

## 🧪 Sample Output 
```text
[ec2-user@ip-172-31-73-60 PySubnet-Linux]$ python3 main.py --mode calc --target 192.168.10.50/24

╔════════════════════════════════════════╗
║      NET-VERIFY: Subnet & Linux Tool   ║
╚════════════════════════════════════════╝


--- NETWORK DETAILS ---
IP Address          : 192.168.10.50
Binary IP           : 11000000.10101000.00001010.00110010
Network Address     : 192.168.10.0
Binary Netmask      : 11111111.11111111.11111111.00000000
CIDR                : /24
Usable Range        : 192.168.10.1 - 192.168.10.254
Hosts               : 254
Private Network     : True
[ec2-user@ip-172-31-73-60 PySubnet-Linux]$ python3 main.py --mode live

╔════════════════════════════════════════╗
║      NET-VERIFY: Subnet & Linux Tool   ║
╚════════════════════════════════════════╝

[*] Mode: LIVE LINUX INTEGRATION
[*] Auto-detecting IP from OS...
[+] Detected: 172.31.73.60/20

--- NETWORK DETAILS ---
IP Address          : 172.31.73.60
Binary IP           : 10101100.00011111.01001001.00111100
Network Address     : 172.31.64.0
Binary Netmask      : 11111111.11111111.11110000.00000000
CIDR                : /20
Usable Range        : 172.31.64.1 - 172.31.79.254
Hosts               : 4,094
Private Network     : True

--- LIVE REACHABILITY CHECK (Linux Ping) ---
Target IP            | Status
-----------------------------------
172.31.64.0          | DOWN
172.31.64.1          | UP (Reachable)
172.31.64.254        | DOWN
172.31.73.60         | UP (Reachable)
[ec2-user@ip-172-31-73-60 PySubnet-Linux]$ python3 main.py --target 10.0.0.1/24 --subnet 27

╔════════════════════════════════════════╗
║      NET-VERIFY: Subnet & Linux Tool   ║
╚════════════════════════════════════════╝


--- NETWORK DETAILS ---
IP Address          : 10.0.0.1
Binary IP           : 00001010.00000000.00000000.00000001
Network Address     : 10.0.0.0
Binary Netmask      : 11111111.11111111.11111111.00000000
CIDR                : /24
Usable Range        : 10.0.0.1 - 10.0.0.254
Hosts               : 254
Private Network     : True

--- SUBNETTING into /27 ---
Network            | Broadcast          | Range Start
-------------------------------------------------------
10.0.0.0           | 10.0.0.31          | 10.0.0.1
10.0.0.32          | 10.0.0.63          | 10.0.0.33
10.0.0.64          | 10.0.0.95          | 10.0.0.65
10.0.0.96          | 10.0.0.127         | 10.0.0.97
10.0.0.128         | 10.0.0.159         | 10.0.0.129
10.0.0.160         | 10.0.0.191         | 10.0.0.161
10.0.0.192         | 10.0.0.223         | 10.0.0.193
10.0.0.224         | 10.0.0.255         | 10.0.0.225
```

## 🧠 Technical Implementation

### Bitwise Logic (Core Algorithm)

The tool calculates network boundaries using bitwise operators, fulfilling the project requirement to "Implement the core algorithm":

  * **Network ID:** `IP_Address & Subnet_Mask`
  * **Broadcast ID:** `Network_ID | (~Subnet_Mask)`

### System Integration

The tool fulfills the "Linux Integration" requirement by using the Python `subprocess` module to wrap standard shell commands:

  * `ip -4 addr show`: Regex parsing used to extract the IP.
  * `ping -c 1 -W 1 <ip>`: Exit codes used to determine UP/DOWN status.

## ⚠️ Troubleshooting

  * **Permission Denied:** If running ping on certain Linux systems fails, run with sudo:
    ```bash
    sudo python3 main.py --mode live
    ```
  * **Windows Users:** The "Live Mode" uses Linux-specific commands (`ip`, `grep`). On Windows, the tool will simulate the output or fallback to calc mode automatically.

## 📜 License
Distributed under the MIT License. See LICENSE for more information.

Copyright (c) 2025 Dev Sharma
