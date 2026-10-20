# vpn-ipsec-ikev2-gre-aaa-siem-dns
VPN IKEv2/IPSec Cisco FortiGate GRE AAA RADIUS LDAP wazuh siem dns private and dns public sever messagier dhcp config on fortigate for vlans

---

## ✅ Features Implemented

### 🔒 VPN
- IKEv2/IPSec tunnel Cisco CSR1000v ↔ FortiGate
- GRE over IPSec
- Encryption: DES-SHA256 DH Group 14
- Pre-shared key authentication
- Policy-based IPSec on FortiGate

### 🌐 Routing
- OSPF Area 0 on all interfaces
- MTU ignore between Cisco and FortiGate
- Static routes for inter-network communication
- GRE tunnel for OSPF transport

### 🔐 AAA Authentication
- FreeRADIUS server (Ubuntu 192.168.40.4)
- RADIUS authentication on CSR1000v
- RADIUS authentication on FortiGate-VPN
- RADIUS authentication on FortiGate-Internal
- Local fallback authentication
- LDAP authentication (Ubuntu 192.168.50.2)
- LDAP users: vpnuser/cisco123

### 🌍 DNS
- Private DNS (BIND9 on LDAP server 192.168.50.2)
  - Zone: ldap.com
  - Records: ldap, mail, ns1
- Public DNS (Windows Server 192.168.10.3)
  - Forward to 8.8.8.8 and 8.8.4.4
  - MX record for mail.ldap.com
- CSR configured as DNS forwarder

### 📧 Mail Server
- MailEnable on Windows Server (192.168.10.3)
- Domain: ldap.com
- SMTP/POP3/IMAP enabled
- Mailbox: vpnuser@ldap.com

### 🛡️ Firewalls
#### FortiGate-VPN (Edge)
- port1: 192.168.20.2 (WAN - link to CSR)
- port2: LAN side
- IPSec VPN policies
- RADIUS authentication
- GRE tunnel interface

#### FortiGate-Internal
- port1: 192.168.30.2 (link to CSR)
- port2: VLAN trunk to Switch1
  - port2.10: 192.168.70.1 (VLAN 10)
  - port2.20: 192.168.80.1 (VLAN 20)
- port3: 192.168.50.1 (LDAP/DNS server)
- port4: 192.168.80.1 (VLAN 80)
- LDAP authentication
- DHCP server for VLANs

### 🖥️ VLANs
| VLAN | ID | Subnet | Gateway | Interface |
|------|----|--------|---------|-----------|
| VLAN 70 | 10 | 192.168.70.0/24 | 192.168.70.1 | port4.10 |
| VLAN 80 | 80 | 192.168.80.0/24 | 192.168.80.1 | port4.20 |

### 📡 DHCP
- VLAN 10: 192.168.70.10 - 192.168.70.200
- VLAN 20: 192.168.80.10 - 192.168.80.200
- DNS: 192.168.50.2 (private) + 8.8.8.8 (public)
- Domain: ldap.com

### 🔍 Wazuh SIEM
- Wazuh server connected to FortiGate-Internal
- Security monitoring and log analysis
- Agents on Windows10 and Ubuntu

---

## 📁 Configurations

### Cisco CSR1000v
- [Full Config](configs/cisco.txt)
- IKEv2 proposal: DES + SHA256 + DH14
- GRE tunnel: 10.0.0.1/30
- OSPF router-id: 1.1.1.1
- AAA with RADIUS fallback to local

### FortiGate-VPN
- [Full Config](configs/fortigate-vpn.txt)
- Policy-based IPSec
- GRE tunnel over port1
- RADIUS authentication
- OSPF on gre-to-CSR interface

### FortiGate-Internal
- [Full Config](configs/fortigate-internal.txt)
- VLAN 10/20/80
- DHCP server
- LDAP authentication
- OSPF on port1
- Wazuh integration

### RADIUS Server (Ubuntu)
- [Config](configs/radius.txt)
- FreeRADIUS 3.x
- Client: CSR (192.168.40.1)
- Client: FortiGate-VPN (192.168.20.2)
- Client: FortiGate-Internal (192.168.30.2)
- User: vpnuser/cisco123

### LDAP/DNS Private Server (Ubuntu)
- [Config](configs/ldap.txt)
- OpenLDAP (slapd)
- Domain: dc=ldap,dc=com
- BIND9 DNS
- Zone: ldap.com
- User: vpnuser/cisco123

### DNS Public/Mail Server (Windows Server)
- [Config](configs/mail.txt)
- Windows DNS Server
- MailEnable Standard
- Domain: ldap.com
- MX: mail.ldap.com → 192.168.10.3

---

## 🌐 IP Addressing Table

| Device | Interface | IP Address | Subnet | Role |
|--------|-----------|------------|--------|------|
| CSR1000v | Gi1 | 192.168.10.1 | /24 | Management |
| CSR1000v | Gi2 | 192.168.20.1 | /24 | WAN/VPN |
| CSR1000v | Gi3 | 192.168.30.1 | /24 | Internal FW |
| CSR1000v | Gi4 | 192.168.40.1 | /24 | RADIUS |
| CSR1000v | Tunnel0 | 10.0.0.1 | /30 | GRE |
| FortiGate-VPN | port1 | 192.168.20.2 | /24 | WAN |
| FortiGate-Internal | port1 | 192.168.30.2 | /24 | Uplink |
| FortiGate-Internal | port2.10 | 192.168.70.1 | /24 | VLAN10 |
| FortiGate-Internal | port2.20 | 192.168.80.1 | /24 | VLAN20 |
| FortiGate-Internal | port3 | 192.168.50.1 | /24 | LDAP |
| RADIUS Server | eth0 | 192.168.40.4 | /24 | RADIUS |
| LDAP/DNS Private | eth0 | 192.168.50.2 | /24 | LDAP/DNS |
| Mail/DNS Public | NIC | 192.168.10.3 | /24 | Mail/DNS |
| VLAN10 clients | - | 192.168.70.10-200 | /24 | LAN |
| VLAN20 clients | - | 192.168.80.10-200 | /24 | LAN |

---

## 📸 Screenshots
- [Topology](screenshots/topology.png)
- [IKEv2 SA](screenshots/ikev2-sa.png)
- [GRE Tunnel](screenshots/gre-tunnel.png)
- [RADIUS Auth](screenshots/radius-auth.png)
- [LDAP Auth](screenshots/ldap-auth.png)
- [OSPF Neighbors](screenshots/ospf-neighbors.png)
- [Wireshark ESP](screenshots/wireshark-esp.png)
- [DHCP Leases](screenshots/dhcp-leases.png)
- [MailEnable](screenshots/mailenable.png)
- [Wazuh Dashboard](screenshots/wazuh.png)

---

## 🛠️ Technologies Used
- Cisco CSR1000v 16.08.01a
- FortiGate VM 7.0.9
- FreeRADIUS 3.x
- OpenLDAP (slapd)
- BIND9 DNS
- MailEnable Standard
- Wazuh SIEM
- GNS3 2.2.x
- VMware Workstation

---

## 👨‍💻 Author
- Project:  VPN IKEv2/IPSec Cisco ↔ FortiGate avec GRE et AAA
