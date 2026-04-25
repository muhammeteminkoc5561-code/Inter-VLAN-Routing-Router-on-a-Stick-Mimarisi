Inter-VLAN Routing (Router on a Stick) Architecture 🚀
This project is a Cisco Packet Tracer simulation designed to isolate departments (VLANs) within enterprise network infrastructures and enable secure communication between these isolated networks via a single physical router.

📌 Project Objective and Scenario
In this corporate network scenario, the IT and Accounting departments are separated into different VLANs to enhance security and network performance. To allow these departments to communicate with each other or external networks, an 802.1Q trunking configuration was implemented using the "Router on a Stick" model.

🧠 Technical Logic & Design
Why did we configure VLANs?
By isolating departmental traffic (IT and Accounting), we ensured data security. This segmentation prevents a potential network issue or security breach in one department from affecting the other.

The "Router on a Stick" Logic
Instead of using a separate physical cable for every VLAN on the router, we divided a single physical interface (Gig0/0) into logical sub-interfaces. This method provides:

Cost Efficiency: Minimizes the use of physical router ports.

Flexibility: Allows adding new VLANs without needing physical hardware changes.

🛠️ Technologies and Protocols Used
VLAN (Virtual Local Area Network): Network isolation (VLAN 10 and VLAN 20).

802.1Q Encapsulation: Tagging traffic passing through trunk ports.

Sub-interfaces: Logical partitioning of a single physical router port.

ICMP (Ping): Used for testing end-to-end network connectivity.

💻 Configuration Summary
1. Switch Configuration (VLAN & Trunk)
VLANs were created on the switch, ports were assigned to relevant departments, and the link to the router was configured as a Trunk port:

Bash
# Creating VLANs
vlan 10
 name IT
vlan 20
 name Accounting

# Setting up Access Ports
interface fastEthernet 0/1
 switchport mode access
 switchport access vlan 10

interface fastEthernet 0/2
 switchport mode access
 switchport access vlan 20

# Setting up the Trunk Port
interface gigabitEthernet 0/1
 switchport mode trunk
2. Router Configuration (Sub-interfaces)
The single physical port on the router was converted into separate logical gateways for each VLAN:

Bash
# Activating the physical interface
interface gigabitEthernet 0/0
 no shutdown

# Sub-interface for IT (VLAN 10)
interface gigabitEthernet 0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

# Sub-interface for Accounting (VLAN 20)
interface gigabitEthernet 0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
🚀 v2.0 Update: DHCP Integration
To accommodate network growth, manual IP assignment (Static IP) was replaced by a DHCP Server configured directly on the Router.

Why DHCP?

Management Ease: Automated the process instead of manually configuring hundreds of devices.

Error Prevention: Prevented "IP Conflict" issues caused by human error.

Bash
# Excluding Gateway IPs from the pool (To prevent conflicts)
ip dhcp excluded-address 192.168.10.1
ip dhcp excluded-address 192.168.20.1

# DHCP Pool for IT Department
ip dhcp pool IT_POOL
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8

# DHCP Pool for Accounting Department
ip dhcp pool ACC_POOL
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8
🔒 v3.0 Update: Secure Remote Management (SSH)
In real-world enterprise environments, remote management is essential. This update secures the device management plane.

Why SSH instead of Telnet?
Telnet transmits data (including usernames and passwords) in clear-text. To prevent network sniffing attacks, SSH (Secure Shell) was configured to encrypt all management traffic.

Bash
# Defining Identity and Domain
hostname CoreRouter
ip domain-name company.local

# Generating RSA Encryption Keys (1024 bit)
crypto key generate rsa 

# Creating a Local Administrator Account (Privilege 15)
username admin privilege 15 secret 123456

# Restricting Virtual Ports (VTY) to SSH only
line vty 0 4
 login local
 transport input ssh
