1. VLANs.
* Admin - 10
* Finance -20
* Credit and Loans - 30
* Reception - 40
* Customer Service - 50
* Marketing - 60
* HR - 70
* IT - 80
* Server Room - 90
* MANAGEMENT - 99

2. Subnets Allocation.
* Administration (/28, VLAN 10)
  Range: 172.16.4.0 - 172.16.4.15
  Network: 172.16.4.0
  Broadcast: 172.16.4.15
  Usable: 172.16.4.1 - 172.16.4.14 (14 IPs)

* Finance (/28, VLAN 20)
  Range: 172.16.4.16 - 172.16.4.31
  Network: 172.16.4.16
  Broadcast: 172.16.4.31
  Usable: 172.16.4.17 - 172.16.4.30 (14 IPs)

* Credit and Loans (/28, VLAN 30)
  Range: 172.16.4.32 - 172.16.4.47
  Network: 172.16.4.32
  Broadcast: 172.16.4.47
  Usable: 172.16.4.33 - 172.16.4.46 (14 IPs)

* Customer Service (/28, VLAN 50)
  Range: 172.16.4.48 - 172.16.4.63
  Network: 172.16.4.48
  Broadcast: 172.16.4.63
  Usable: 172.16.4.49 - 172.16.4.62 (14 IPs)

* IT (/28, VLAN 80)
  Range: 172.16.4.64 - 172.16.4.79
  Network: 172.16.4.64
  Broadcast: 172.16.4.79
  Usable: 172.16.4.65 - 172.16.4.78 (14 IPs)

* Networking Devices (/28, VLAN 99)
  Range: 172.16.4.80 - 172.16.4.95
  Network: 172.16.4.80
  Broadcast: 172.16.4.95
  Usable: 172.16.4.81 - 172.16.4.94 (14 IPs)

* Reception (/29, VLAN 40)
  Range: 172.16.4.96 - 172.16.4.103
  Network: 172.16.4.96
  Broadcast: 172.16.4.103
  Usable: 172.16.4.97 - 172.16.4.102 (6 IPs)

* Marketing (/29, VLAN 60)
  Range: 172.16.4.104 - 172.16.4.111
  Network: 172.16.4.104
  Broadcast: 172.16.4.111
  Usable: 172.16.4.105 - 172.16.4.110 (6 IPs)

* HR (/29, VLAN 70)
  Range: 172.16.4.112 - 172.16.4.119
  Network: 172.16.4.112
  Broadcast: 172.16.4.119
  Usable: 172.16.4.113 - 172.16.4.118 (6 IPs)

* Server Room (/29, VLAN 90)
  Range: 172.16.4.120 - 172.16.4.127
  Network: 172.16.4.120
  Broadcast: 172.16.4.127
  Usable: 172.16.4.121 - 172.16.4.126 (6 IPs)


ACCESS SWITCHES CONFIGURATIONS.
1. Enter enable mode.
enable
configure terminal

2. Configure switch hostname.
hostname IT-SRV-SW

3. Configure banner message for security and legal purposes.
banner motd #Authorized Access Only: SACCO IT & Server Room Switch. Unauthorized access is prohibited.#

4. Create local authentication username password pair.
username mzee privilege 15 secret mzee@2025
username jomba privilege 15 secret jomba@2025
enable secret sacco@enable

5. Configure secure access for remote management using SSH and use local auth.
line vty 0 15
login local
transport input ssh
exit
ip domain-name sacco.local
crypto key generate rsa 1024

6. Configure line console to use local auth.
line console 0
login local
exit

7. Encrypt all passwords.
service password-encryption

8. Configure a management vlan and assign it an ip add and default gateway.
vlan 99
name MANAGEMENT

interface vlan99
ip address 172.16.4.138 255.255.255.240
no shutdown
ip default-gateway 172.16.4.133

9. Configure VLANS for departments.
vlan 80
name IT
vlan 90
name SERVER_ROOM

10. Configure switch interfaces (access and trunk), port security and portfast.
interface range fastEthernet0/3 - 13
switchport mode access
switchport access vlan 80
switchport port-security
switchport port-security maximum 2
switchport port-security violation restrict
switchport port-security mac-address sticky
spanning-tree portfast
description ## IT_DATA ##
no shutdown

interface range fastEthernet0/14 - 24
switchport mode access
switchport access vlan 90
switchport port-security
switchport port-security maximum 2
switchport port-security violation restrict
switchport port-security mac-address sticky
spanning-tree portfast
description ## SERVER_ROOM_DATA ##
no shutdown

interface range fastEthernet0/1 - 2
switchport mode trunk
switchport trunk allowed vlan 80,90,99
description ## UPLINK_TO_DIST_SW ##
no shutdown

11. Shutdown interfaces not in use for security.
int range f0/14-20
shutdown
description ## Not in use ##

12. Save the configurations.
end
write memory