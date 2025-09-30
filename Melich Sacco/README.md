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