---
title: 配置与域无关的工作组可用性组
description: 了解如何在 Azure 中的 SQL Server 虚拟机上配置独立于活动目录域的工作组"始终打开可用性"组。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122670"
---
# <a name="configure-a-workgroup-availability-group"></a>配置工作组可用性组 

本文介绍使用始终打开的可用性组创建活动目录域无关群集所需的步骤;这也称为工作组群集。 本文重点介绍与准备和配置工作组和可用性组相关的步骤，并介绍其他文章中介绍的步骤，例如如何创建群集或部署可用性组。 


## <a name="prerequisites"></a>先决条件

要配置工作组可用性组，您需要以下操作：
- 至少两个运行 SQL Server 2016（或更高）的 Windows Server 2016（或更高）虚拟机，使用静态 IP 地址部署到相同的可用性集或不同的可用性区域。 
- 子网上至少有 4 个可用 IP 地址的本地网络。 
- 管理员组中每台计算机上的帐户，在 SQL Server 中也具有系统管理员权限。 
- 打开端口：TCP 1433、TCP 5022、TCP 59999。 

作为参考，本文使用以下参数，但可以根据需要进行修改： 

| **名称** | **参数** |
| :------ | :---------------------------------- |
| **节点1**   | AGNode1 （10.0.0.4） |
| **Node2**   | AGNode2 （10.0.0.5） |
| **群集名称** | AGWGAG （10.0.0.6） |
| **侦听器** | AGListener （10.0.0.7） | 
| **DNS 后缀** | ag.wgcluster.example.com | 
| **工作组名称** | AGWork组 | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>设置 DNS 后缀 

在此步骤中，为两台服务器配置 DNS 后缀。 例如，`ag.wgcluster.example.com` 。 这允许您将要连接到的对象的名称用作网络中完全限定的地址，例如`AGNode1.ag.wgcluster.example.com`。 

要配置 DNS 后缀，请按照以下步骤操作：

1. RDP 进入您的第一个节点并打开服务器管理器。 
1. 选择**本地服务器**，然后在**计算机名称**下选择虚拟机的名称。 
1. 选择 **"更改..."** 下**要重命名此计算机...** 
1. 将工作组名称的名称更改为有意义的内容，例如`AGWORKGROUP`： 

   ![更改工作组名称](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. 选择 **"更多..."** 以打开**DNS 后缀和 NetBIOS 计算机名称**对话框。 
1. 在此**计算机的主 DNS 后缀**下键入 DNS 后缀的名称，例如`ag.wgcluster.example.com`，然后选择 **"确定**" ： 

   ![添加 DNS 后缀](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. 确认 **"完整计算机名称**"现在显示 DNS 后缀，然后选择 **"确定"** 以保存更改： 

   ![添加 DNS 后缀](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. 系统提示您重新启动服务器。 
1. 在用于可用性组的任何其他节点上重复这些步骤。 

## <a name="edit-host-file"></a>编辑主机文件

由于没有活动目录，因此无法对窗口连接进行身份验证。 因此，通过使用文本编辑器编辑主机文件来分配信任。 

要编辑主机文件，请按照以下步骤操作：

1. RDP 进入虚拟机。 
1. 使用**文件资源管理器**转到`c:\windows\system32\drivers\etc`。 
1. 右键单击**主机**文件，然后使用**记事本**（或任何其他文本编辑器）打开该文件。
1. 在文件末尾，以类似的形式`IP Address, DNS Suffix #comment`为每个节点、可用性组和侦听器添加一个条目： 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![将 IP 地址、群集和侦听器的条目添加到主机文件](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>设置权限

由于没有要管理权限的活动目录，因此需要手动允许非内置本地管理员帐户创建群集。 

为此，请在每个节点上的管理 PowerShell 会话中运行以下 PowerShell cmdlet： 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>创建故障转移群集

在此步骤中，您将创建故障转移群集。 如果您不熟悉这些步骤，则可以从[故障转移群集教程](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct)中遵循这些步骤。

本教程与工作组群集应执行哪些工作之间的显著差异：
- 运行群集验证时取消选中**存储**和**存储空间直接**。 
- 将节点添加到群集时，添加完全限定的名称，例如：
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- 取消选中**将所有符合条件的存储添加到群集**。 

创建群集后，分配静态群集 IP 地址。 为此，请执行下列步骤：

1. 在其中一个节点上，打开**故障转移群集管理器**，选择群集，右键单击**群集核心资源**下**的名称：\<群集nam>，** 然后选择**属性**。 

   ![群集名称的启动属性](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. 选择**IP 地址**下的 IP 地址，然后选择 **"编辑**"。 
1. 选择 **"使用静态**"，提供群集的 IP 地址，然后选择 **"确定**" ： 

   ![为群集提供静态 IP 地址](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. 验证您的设置是否正确，然后选择 **"确定"** 以保存它们：

   ![验证群集属性](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>创建云见证 

在此步骤中，配置云共享见证。 如果您不熟悉这些步骤，请参阅[故障转移群集教程](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness)。 

## <a name="enable-availability-group-feature"></a>启用可用性组功能 

在此步骤中，启用可用性组功能。 如果您不熟悉这些步骤，请参阅[可用性组教程](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups)。 

## <a name="create-keys-and-certificate"></a>创建密钥和证书

在此步骤中，创建 SQL 登录在加密终结点上使用的证书。 在每个节点上创建一个文件夹来保存证书备份，如`c:\certs`。 

要配置第一个节点，请按照以下步骤操作： 

1. 打开**SQL 服务器管理工作室**并连接到第一个节点，`AGNode1`如 。 
1. 在更新为复杂和安全的密码后打开 **"新查询**"窗口并运行以下 Transact-SQL （T-SQL） 语句：

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

1. 接下来，创建 HADR 终结点，并通过运行此 Transact-SQL （T-SQL） 语句使用证书进行身份验证：

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

1. 使用**文件资源管理器**转到证书所在的文件位置，例如`c:\certs`。 
1. 手动复制证书（如`AGNode1Cert.crt`，从第一个节点，并将其传输到第二个节点上的同一位置）。 

要配置第二个节点，请按照以下步骤操作： 

1. 使用**SQL 服务器管理工作室**连接到第二个节点，`AGNode2`如 。 
1. 在新**查询**窗口中，在更新为复杂和安全的密码后运行以下 Transact-SQL （T-SQL） 语句： 

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

1. 接下来，创建 HADR 终结点，并通过运行此 Transact-SQL （T-SQL） 语句使用证书进行身份验证：

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

1. 使用**文件资源管理器**转到证书所在的文件位置，例如`c:\certs`。 
1. 手动从第二个节点复制证书，如`AGNode2Cert.crt`，并将其传输到第一个节点上的同一位置。 

如果群集中存在任何其他节点，请在那里重复这些步骤，修改相应的证书名称。 

## <a name="create-logins"></a>创建登录名

证书身份验证用于跨节点同步数据。 为此，请为其他节点创建登录名，为登录创建用户，为登录创建证书以使用备份证书，然后在镜像终结点上授予连接。 

为此，请首先在第一个节点上运行以下 Transact-SQL （T-SQL） 查询，例如`AGNode1`： 

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

接下来，在第二个节点上运行以下 Transact SQL （T-SQL） 查询，`AGNode2`例如： 

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

如果群集中存在任何其他节点，请在那里重复这些步骤，修改相应的证书和用户名。 

## <a name="configure-availability-group"></a>配置可用性组

在此步骤中，配置可用性组，并将数据库添加到其中。 此时不要创建侦听器。 如果您不熟悉这些步骤，请参阅[可用性组教程](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group)。 请务必启动故障转移和故障转移，以验证一切是否正常工作。 

   > [!NOTE]
   > 如果在同步过程中出现故障，您可能需要授予`NT AUTHORITY\SYSTEM`sysadmin 权限才能在第一个节点上创建群集资源，例如`AGNode1`临时节点。 

## <a name="configure-load-balancer"></a>配置负载均衡器

在最后一步中，使用[Azure 门户](virtual-machines-windows-portal-sql-alwayson-int-listener.md)或[PowerShell](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)配置负载均衡器


## <a name="next-steps"></a>后续步骤

您还可以使用 Az [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md)配置可用性组。 


