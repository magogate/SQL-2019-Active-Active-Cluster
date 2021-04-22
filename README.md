# SQL-2019-Active-Active-Cluster
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
2. Change machine name to
3. Allocate 
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
### 2. Domain Controller Configurations   
#### Installation
    1. Open Server Manager
    2. Local Server --> Manage --> Add Roles & Features
    3. Installation Type --> Role Based Installation
    4. Select local server - this is a default option
    5. Server Roles --> Select - Active Directory Domain Services 
    6. Install
#### Configuring Active Directory
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
#### Validating Active Directory 
    1. Open "Active Directory Users and Computers"
