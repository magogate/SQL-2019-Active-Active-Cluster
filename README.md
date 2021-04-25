# SQL-2019-Active-Active-Cluster
## Videos
1. SQL Server 2019 Cluster - 1 : Installation of Win Server 2019 https://youtu.be/xJ_WJ_p0qjk
2. SQL Server 2019 Cluster - 2 : Configuring Active Directory Domain Controller https://youtu.be/oWJPw8WTW9c
3. SQL Server 2019 Cluster - 3 : Configuring of Node1 & Node2 https://youtu.be/LYLVX24hPg8

### How to configure SQL Server 2019 Active / Active Cluster on Windows Server 2019 Cluster

<img src="ActiveActiveSQLCluster.gif" alt="SQL Cluster">

# Intruduction
In this video series we are going to see how to configure SQL Server 2019 Active / Active Cluster. Following are the pre-requisit for that

## Softwares needed
1. Windows Server 2019 Evaluation Edition, which you can download from 
    https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2019
2. Vritualization Software - You can download VMWare Workstattion for free from following link for non-prod environment 
    https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html


# Configuring Windows Server Cluster
Very first step in configuring SQL Server 2019 cluster is to have Windows Cluster first. Now there are many steps involved while creating Windows Cluster itself. Following are the steps which you can follow in-order to configure Windows Server Cluster

## 1. Installation of Windows Server 2019 Server
1. Install Windows Server 2019 by creating a Virtual Machine
2. Change machine name 
3. Allocate IP Address
   #### Computer Name 
        1. This PC --> Properties --> Advanced System Settings --> Computer Name (gogate-dc-1)
   #### IP Addresses
        1. Control Panel --> Network & Internet --> Network and Sharing Center --> Ethernet0 --> Properties --> Internet Protocol Verstion 4 --> (TCP/IPv4)
           - Static IP Address : 192.168.80.10 (https://www.paessler.com/it-explained/ip-address) & (https://www.rapidtables.com/convert/number/binary-to-decimal.html)
           - Subnet Mask : 255.255.255.0 (https://www.paessler.com/it-explained/ip-address)
           - Default Gateway : 192.168.80.2 [Same for all VMs] (https://en.wikipedia.org/wiki/Default_gateway)
           - Preferred DNS Server : 192.168.80.10 [Same for all VMs] (https://www.cloudflare.com/learning/dns/what-is-a-dns-server/)
           - Alternet DNS Server : 192.168.80.2 
   #### Disable Firewall
        1. Control Panel --> System and Security --> Windows Defender Firewall --> Turn Off Windows Defender Firewall
## 2. Domain Controller Configurations   
### Installation of Active Directory
    1. Open Server Manager
    2. Local Server --> Manage --> Add Roles & Features
    3. Installation Type --> Role Based Installation
    4. Select local server - this is a default option
    5. Server Roles --> Select - Active Directory Domain Services 
    6. Install
### Configuring Active Directory Domain Service
    1. Click on AD-DS Role at Server Manager
    2. Click More
    3. Select Action - Promote server to domain controller
    4. Add a new forest (Since we don't have any existing domain)
    5. Give domain name as - gogates.local [you can give any name which you want eg. abc.xyz.pqr]
    6. Click Next
    7. Specify DSRM (directory service recovery mode) password
    8. No need to create DNS deligation
    9. NetBIOS domain name -- keep it default (it will appear as gogates as we had mentioned domain as gogates.local)
    10. Paths - Keep it default
    11. Install
    12. Restart & Login with Doamin User this time
### Validating Active Directory 
    1. Open "Active Directory Users and Computers"

## 3. Installation of 2 Nodes
### Installation of node-1
1. Install Windows Server 2019 by creating a Virtual Machine
2. Change machine name 
3. Allocate IP Address
   #### Computer Name 
        1. This PC --> Properties --> Advanced System Settings --> Computer Name (gogate-node-1)
   #### IP Addresses
        1. Control Panel --> Network & Internet --> Network and Sharing Center --> Ethernet0 --> Properties --> Internet Protocol Verstion 4 --> (TCP/IPv4)
           - Static IP Address : 192.168.80.15 (https://www.paessler.com/it-explained/ip-address) & (https://www.rapidtables.com/convert/number/binary-to-decimal.html)
           - Subnet Mask : 255.255.255.0 (https://www.paessler.com/it-explained/ip-address)
           - Default Gateway : 192.168.80.2 [Same for all VMs] (https://en.wikipedia.org/wiki/Default_gateway)
           - Preferred DNS Server : 192.168.80.10 [Same for all VMs] (https://www.cloudflare.com/learning/dns/what-is-a-dns-server/)
           - Alternet DNS Server : Keep blank 
   #### Disable Firewall
        1. Control Panel --> System and Security --> Windows Defender Firewall --> Turn Off Windows Defender Firewall
 ### Installation of node-2
1. Install Windows Server 2019 by creating a Virtual Machine
2. Change machine name 
3. Allocate IP Address
   #### Computer Name 
        1. This PC --> Properties --> Advanced System Settings --> Computer Name (gogate-node-2)
   #### IP Addresses
        1. Control Panel --> Network & Internet --> Network and Sharing Center --> Ethernet0 --> Properties --> Internet Protocol Verstion 4 --> (TCP/IPv4)
           - Static IP Address : 192.168.80.20 (https://www.paessler.com/it-explained/ip-address) & (https://www.rapidtables.com/convert/number/binary-to-decimal.html)
           - Subnet Mask : 255.255.255.0 (https://www.paessler.com/it-explained/ip-address)
           - Default Gateway : 192.168.80.2 [Same for all VMs] (https://en.wikipedia.org/wiki/Default_gateway)
           - Preferred DNS Server : 192.168.80.10 [Same for all VMs] (https://www.cloudflare.com/learning/dns/what-is-a-dns-server/)
           - Alternet DNS Server : Keep blank
   #### Disable Firewall
        1. Control Panel --> System and Security --> Windows Defender Firewall --> Turn Off Windows Defender Firewall

## 4. Adding nodes to Domain
1. Validate if you can ping to Domain from both the nodes - ping gogates.local
2. Assign domain name for both nodes 
   - This PC --> Properties --> Advanced System Settings --> Member Of Domain - gogates.local
   - Specify credentials for Domain Admin - gogates\Administrator & P@ssword#123
   - Restart server
   - Follow same steps for both nodes
   - While logging you should be able to login as Domain Administrator user instead of local Administrator
   - Validate nodes in Domain Controller using "Active Directory Users and Computers"

## 5. Installation of iScassi Target
As mentioned in very first video and also in image, DC Server / Node will also act as Storage or SAN. We will create 6 drives there as below
1. 2 Data Drives (5 GB Each)
2. 2 Log Drives (3 GB Each)
3. 1 Temp Drives (2 GB Each)

These drives will share with other nodes, wherein we will actually install & configure SQL Server 2019 Active \ Active Cluster.

Now, in order to create & share \ present these drives (iscasi luns) to other nodes, we need to follow following steps
### a. Installation of iSCSI Target Server
        1. Open Server Manager
        2. Local Server --> Manage --> Add Roles & Features
        3. Installation Type --> Role Based Installation
        4. Select local server - this is a default option
        5. Server Roles --> Select - File & Storage Services --> iSCSI Target Server
        6. Install        
### b. Add NIC Cards to all 3 nodes in network for iSCSI communication
        1. Player --> Manage --> Virtual Machine Settings
        2. Click Add --> Select Network Adapter
        3. Assign static IP Address for all 3 nodes as below
           - for DC             -- 10.0.0.10
           - for gogate-node-1  -- 10.0.0.15
           - for gogate-node-2  -- 10.0.0.20
        4. Disable Windows Firewall
        5. Check the connectivity from DC
    
