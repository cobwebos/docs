---
title: 配置与域无关的工作组可用性组
description: 了解如何在 Azure 中的 SQL Server 虚拟机上配置与 Active Directory 域无关的工作组 Always On 可用性组。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 72c04cf5e3e5fbdeac2d267dfc7b2703bd37a1c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77122670"
---
# <a name="configure-a-workgroup-availability-group"></a>配置工作组可用性组 

本文将会说明创建与 Active Directory 域无关的群集（该群集启用了 Always On 可用性组，也称为工作组群集）所要执行的步骤。 本文将重点说明与准备和配置工作组及可用性组相关的步骤，而不会重述其他文章中已介绍过的步骤，例如创建群集或部署可用性组。 


## <a name="prerequisites"></a>先决条件

若要配置工作组可用性组，需要准备好以下各项：
- 至少两个运行 SQL Server 2016（或更高版本）的 Windows Server 2016（或更高版本）虚拟机，这些虚拟机已使用静态 IP 地址部署到同一个可用性集或不同的可用性区域。 
- 一个本地网络，其子网中至少有 4 个可用 IP 地址。 
- 管理员组中的每台计算机上各有一个帐户，该帐户在 SQL Server 中也拥有 sysadmin 权限。 
- 打开端口：TCP 1433、TCP 5022、TCP 59999。 

为便于参考，本文使用了以下参数，但可根据需要对其进行修改： 

| **名称** | **参数** |
| :------ | :---------------------------------- |
| **Node1**   | AGNode1 (10.0.0.4) |
| **Node2**   | AGNode2 (10.0.0.5) |
| **群集名称** | AGWGAG (10.0.0.6) |
| **侦听器** | AGListener (10.0.0.7) | 
| **DNS 后缀** | ag.wgcluster.example.com | 
| **工作组名称** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>设置 DNS 后缀 

此步骤为两个服务器配置 DNS 后缀。 例如，`ag.wgcluster.example.com`。 这样，你便可以使用所要连接到的对象的名称作为网络中的完全限定地址，例如 `AGNode1.ag.wgcluster.example.com`。 

若要配置 DNS 后缀，请执行以下步骤：

1. 通过 RDP 连接到第一个节点，然后打开服务器管理器。 
1. 选择“本地服务器”，然后在“计算机名”下选择你的虚拟机名称。   
1. 选择“若要重命名此计算机...”下的“更改...”。   
1. 将工作组名称更改为有意义的名称，例如 `AGWORKGROUP`： 

   ![更改工作组名称](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. 选择“更多...”打开“DNS 后缀和 NetBIOS 计算机名”对话框。   
1. 在“此计算机的主 DNS 后缀”下键入 DNS 后缀的名称（例如 `ag.wgcluster.example.com`），然后选择“确定”：   

   ![添加 DNS 后缀](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. 确认“计算机全名”现在显示了 DNS 后缀，然后选择“确定”以保存更改：   

   ![添加 DNS 后缀](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. 根据提示重新启动服务器。 
1. 在要用于可用性组的任何其他节点上重复这些步骤。 

## <a name="edit-host-file"></a>编辑主机文件

由于没有 Active Directory，无法对 Windows 连接进行身份验证。 因此，请通过使用文本编辑器编辑主机文件来分配信任。 

若要编辑主机文件，请执行以下步骤：

1. 通过 RDP 连接到虚拟机。 
1. 使用“文件资源管理器”转到 `c:\windows\system32\drivers\etc`。  
1. 右键单击“hosts”文件，并选择使用**记事本**（或任何其他文本编辑器）打开该文件。 
1. 在该文件的末尾，以 `IP Address, DNS Suffix #comment` 格式为每个节点、可用性组和侦听器各添加一个条目，如下所示： 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![在主机文件中添加 IP 地址、群集和侦听器的条目](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>设置权限

由于未使用 Active Directory 来管理权限，因此需要手动允许非内置的本地管理员帐户创建群集。 

为此，请在每个节点上的管理 PowerShell 会话中运行以下 PowerShell cmdlet： 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>创建故障转移群集

此步骤将创建故障转移群集。 如果你不熟悉这些步骤，请参阅[故障转移群集教程](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct)。

该教程中的步骤与应该对工作组群集执行的操作存在很大的差别：
- 运行群集验证时取消选中“存储”和“存储空间直通”。   
- 将节点添加到群集时，请添加完全限定的名称，例如：
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- 取消选中“将所有符合条件的存储添加到群集中”。  

创建群集后，分配静态群集 IP 地址。 为此，请执行以下步骤：

1. 在某个节点上打开“故障转移群集管理器”，选择群集，右键单击“群集核心资源”下的“名称:   \<ClusterNam>”，然后选择“属性”。   

   ![针对该群集名称启动属性配置](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. 在“IP 地址”下选择 IP 地址，然后选择“编辑”。   
1. 选择“使用静态”，提供群集的 IP 地址，然后选择“确定”：   

   ![为群集提供静态 IP 地址](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. 验证设置是否正确，然后选择“确定”以保存设置： 

   ![验证群集属性](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>创建云见证 

此步骤配置云共享见证。 如果你不熟悉这些步骤，请参阅[故障转移群集教程](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness)。 

## <a name="enable-availability-group-feature"></a>启用可用性组功能 

此步骤启用可用性组功能。 如果你不熟悉这些步骤，请参阅[可用性组教程](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups)。 

## <a name="create-keys-and-certificate"></a>创建密钥和证书

此步骤创建 SQL 登录名在加密的终结点上使用的证书。 在每个节点上创建一个文件夹（例如 `c:\certs`）用于保存证书备份。 

若要配置第一个节点，请执行以下步骤： 

1. 打开“SQL Server Management Studio”并连接到第一个节点，例如 `AGNode1`。  
1. 打开“新建查询”窗口，在更新为复杂的安全密码后运行以下 Transact-SQL (T-SQL) 语句： 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. 接下来创建 HADR 终结点，并运行以下 Transact-SQL (T-SQL) 语句来使用证书进行身份验证：

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. 使用“文件资源管理器”转到证书所在的文件位置，例如 `c:\certs`。  
1. 在第一个节点上手动创建证书的副本（例如 `AGNode1Cert.crt`），并将其传输到第二个节点上的同一位置。 

若要配置第二个节点，请执行以下步骤： 

1. 使用“SQL Server Management Studio”连接到第二个节点，例如 `AGNode2`。  
1. 在“新建查询”窗口中，在更新为复杂的安全密码后运行以下 Transact-SQL (T-SQL) 语句：  

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. 接下来创建 HADR 终结点，并运行以下 Transact-SQL (T-SQL) 语句来使用证书进行身份验证：

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. 使用“文件资源管理器”转到证书所在的文件位置，例如 `c:\certs`。  
1. 在第二个节点上手动创建证书的副本（例如 `AGNode2Cert.crt`），并将其传输到第一个节点上的同一位置。 

如果群集中包含任何其他节点，请在这些节点上重复上述步骤，同时请修改相应的证书名称。 

## <a name="create-logins"></a>创建登录名

证书身份验证将用来跨节点同步数据。 为实现此目的，请为另一个节点创建登录名，为该登录名创建用户，为该登录名创建证书以使用备份证书，然后在镜像终结点上授予连接权限。 

为此，请先在第一个节点（例如 `AGNode1`）上运行以下 Transact-SQL (T-SQL) 查询： 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

接下来，在第二个节点（例如 `AGNode2`）上运行以下 Transact-SQL (T-SQL) 查询： 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

如果群集中包含任何其他节点，请在这些节点上重复上述步骤，同时请修改相应的证书名称和用户名。 

## <a name="configure-availability-group"></a>配置可用性组

此步骤配置可用性组并向其添加数据库。 目前请不要创建侦听器。 如果你不熟悉这些步骤，请参阅[可用性组教程](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group)。 确保启动故障转移和故障回复，以验证一切是否按预期方式进行。 

   > [!NOTE]
   > 如果在同步过程中发生失败，可能需要暂时授予 `NT AUTHORITY\SYSTEM` sysadmin 权限以便在第一个节点（例如 `AGNode1`）上创建群集资源。 

## <a name="configure-load-balancer"></a>配置负载均衡器

最后一个步骤使用 [Azure 门户](virtual-machines-windows-portal-sql-alwayson-int-listener.md)或 [PowerShell](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) 配置负载均衡器


## <a name="next-steps"></a>后续步骤

还可以使用 [Az SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) 配置可用性组。 


