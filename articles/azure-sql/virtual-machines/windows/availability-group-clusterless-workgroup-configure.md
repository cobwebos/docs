---
title: 配置域独立工作组可用性组
description: 了解如何在 Azure 中的 SQL Server 虚拟机上配置 Active Directory 域无关的工作组 Always On 可用性组。
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
ms.openlocfilehash: 93819332def05022272eabc130e0f2240938f244
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955499"
---
# <a name="configure-a-workgroup-availability-group"></a>配置工作组可用性组 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍了使用 Always On 可用性组创建 Active Directory 域独立群集所需的步骤；这也称为工作组群集。 本文重点介绍与准备和配置工作组和可用性组相关的步骤，略过了其他文章中已介绍的步骤，例如如何创建群集或部署可用性组。 


## <a name="prerequisites"></a>先决条件

若要配置工作组可用性组，需要以下内容：
- 至少两台运行 SQL Server 2016（或更高版本）的 Windows Server 2016（或更高版本）虚拟机，并使用静态 IP 地址将其部署到相同的可用性集或不同的可用性区域。 
- 在子网上具有至少 4 个可用 IP 地址的本地网络。 
- 在管理员组中的每台计算机上都有一个帐户，该帐户在 SQL Server 内也拥有 sysadmin 权限。 
- 打开端口：TCP 1433、TCP 5022、TCP 59999。 

作为参考，本文中使用了以下参数，但可以根据需要对参数进行修改： 

| **名称** | **Parameter** |
| :------ | :---------------------------------- |
| **Node1**   | AGNode1 (10.0.0.4) |
| **Node2**   | AGNode2 (10.0.0.5) |
| **群集名称** | AGWGAG (10.0.0.6) |
| **侦听器** | AGListener (10.0.0.7) | 
| **DNS 后缀** | ag.wgcluster.example.com | 
| **工作组名称** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-a-dns-suffix"></a>设置 DNS 后缀 

在此步骤中，为这两个服务器配置 DNS 后缀。 例如，`ag.wgcluster.example.com`。 这样，你就可以使用想要连接到的对象的名称作为网络中的完全限定地址，如 `AGNode1.ag.wgcluster.example.com`。 

若要配置 DNS 后缀，请执行以下步骤：

1. RDP 连接到第一个节点并打开服务器管理器。 
1. 选择“本地服务器”，然后在“计算机名称”下选择虚拟机的名称 。 
1. 选择“重命名此计算机…”下的“更改…” 。 
1. 将工作组名称的名称更改为有意义的名称，例如 `AGWORKGROUP`： 

   ![更改工作组名称](./media/availability-group-clusterless-workgroup-configure/1-change-workgroup-name.png)

1. 选择“更多…”以打开“DNS 后缀和 NetBIOS 计算机名”对话框 。 
1. 在“此计算机的主 DNS 后缀”下键入 DNS 后缀的名称，例如 `ag.wgcluster.example.com`，然后选择“确定” ： 

   ![添加 DNS 后缀](./media/availability-group-clusterless-workgroup-configure/2-add-dns-suffix.png)

1. 确认“完整计算机名”现在已显示 DNS 后缀，然后选择“确定”保存更改 ： 

   ![添加 DNS 后缀](./media/availability-group-clusterless-workgroup-configure/3-confirm-full-computer-name.png)

1. 当系统提示重启服务器时，请进行重启。 
1. 在要用于可用性组的任何其他节点上重复这些步骤。 

## <a name="edit-a-host-file"></a>编辑主机文件

由于没有 Active Directory，因此无法对 Windows 连接进行身份验证。 因此，请通过使用文本编辑器编辑主机文件来分配信任。 

若要编辑主机文件，请执行以下步骤：

1. RDP 连接到你的虚拟机。 
1. 使用文件资源管理器以转到 `c:\windows\system32\drivers\etc`。 
1. 右键单击 hosts 文件，然后使用记事本（或任何其他文本编辑器）打开该文件 。
1. 在该文件末尾，以 `IP Address, DNS Suffix #comment` 的形式为每个节点、可用性组和侦听器添加一个条目，如下所示： 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![将 IP 地址、群集和侦听器的条目添加到主机文件](./media/availability-group-clusterless-workgroup-configure/4-host-file.png)

## <a name="set-permissions"></a>设置权限

由于没有用于管理权限的 Active Directory，因此需要手动允许非内置的本地管理员帐户创建群集。 

若要执行此操作，请在每个节点上的 PowerShell 管理会话中运行以下 PowerShell cmdlet： 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>创建故障转移群集

在此步骤中，将创建故障转移群集。 如果尚不熟悉这些步骤，可参阅[故障转移群集教程](failover-cluster-instance-storage-spaces-direct-manually-configure.md)执行这些步骤。

教程与应为工作组群集执行的操作之间存在明显差异：
- 运行群集验证时，取消选中“存储”和“存储空间直通” 。 
- 将节点添加到群集时，请添加完全限定的名称，例如：
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- 取消选中“将所有符合条件的存储添加到群集”。 

创建群集后，请分配静态群集 IP 地址。 为此，请执行下列步骤：

1. 在其中一个节点上，打开“故障转移群集管理器”，选择群集，右键单击“群集核心资源”下的“名称: \<ClusterNam>”，然后选择“属性”   。 

   ![启动群集名称的属性](./media/availability-group-clusterless-workgroup-configure/5-launch-cluster-name-properties.png)

1. 选择“IP 地址”下的 IP 地址，然后选择“编辑” 。 
1. 选择“使用静态”，提供群集的 IP 地址，然后选择“确定” ： 

   ![提供群集的静态 IP 地址](./media/availability-group-clusterless-workgroup-configure/6-provide-static-ip-for-cluster.png)

1. 验证是否已正确进行设置，然后选择“确定”以保存设置：

   ![验证群集属性](./media/availability-group-clusterless-workgroup-configure/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>创建云见证 

在此步骤中，配置云共享见证。 如果你不熟悉这些步骤，请参阅[为故障转移群集部署云见证](/windows-server/failover-clustering/deploy-cloud-witness)。 

## <a name="enable-the-availability-group-feature"></a>启用可用性组功能 

在此步骤中，启用可用性组功能。 如果尚不熟悉这些步骤，请参阅[可用性组教程](availability-group-manually-configure-tutorial.md#enable-availability-groups)。 

## <a name="create-keys-and-certificates"></a>创建密钥和证书

在此步骤中，创建 SQL 登录在加密的终结点上使用的证书。 在每个节点上创建一个文件夹来保存证书备份，如 `c:\certs`。 

若要配置第一个节点，请执行以下步骤： 

1. 打开 SQL Server Management Studio 并连接到第一个节点，例如 `AGNode1`。 
1. 更新为复杂且安全的密码后，打开“新建查询”窗口并运行以下 Transact-SQL (T-SQL) 语句：

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

1. 接下来，创建 HADR 终结点，并通过运行以下 Transact-SQL (T-SQL) 语句使用证书进行身份验证：

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

1. 使用文件资源管理器以转到证书所在的文件位置，如 `c:\certs`。 
1. 从第一个节点手动生成证书副本（如 `AGNode1Cert.crt`），并将其传输到第二个节点上的同一位置。 

若要配置第二个节点，请执行以下步骤： 

1. 使用 SQL Server Management Studio 连接到第二个节点，例如 `AGNode2`。 
1. 更新为复杂且安全的密码后，在“新建查询”窗口中运行以下 Transact-SQL (T-SQL) 语句： 

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

1. 接下来，创建 HADR 终结点，并通过运行以下 Transact-SQL (T-SQL) 语句使用证书进行身份验证：

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

1. 使用文件资源管理器以转到证书所在的文件位置，如 `c:\certs`。 
1. 从第二个节点手动生成证书副本（如 `AGNode2Cert.crt`），并将其传输到第一个节点上的同一位置。 

如果群集中有任何其他节点，请重复执行这些步骤，并修改各自的证书名称。 

## <a name="create-logins"></a>创建登录名

证书身份验证用于跨节点同步数据。 为此，请为其他节点创建一个登录，为该登录创建一个用户，为该登录创建一个证书以使用备份证书，然后在镜像终结点上授予连接。 

若要执行此操作，请首先在第一个节点（如 `AGNode1`）上运行以下 Transact-SQL (T-SQL) 查询： 

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

接下来，在第二个节点（如 `AGNode2`）上运行以下 Transact-SQL (T-SQL) 查询： 

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

如果群集中有任何其他节点，请同样在此处重复这些步骤，并修改各自的证书名称及用户名。 

## <a name="configure-an-availability-group"></a>配置可用性组

在此步骤中，配置可用性组，并向其添加数据库。 此时请不要创建侦听器。 如果尚不熟悉这些步骤，请参阅[可用性组教程](availability-group-manually-configure-tutorial.md#create-the-availability-group)。 请确保启动故障转移和故障回复，以便验证所有项是否均正常工作。 

   > [!NOTE]
   > 如果在同步过程中出现故障，则可能需要授予 `NT AUTHORITY\SYSTEM` sysadmin 权限才能在第一个节点（如 `AGNode1`）上临时创建群集资源。 

## <a name="configure-a-load-balancer"></a>配置负载均衡器

在最后一步中，使用[Azure 门户](availability-group-load-balancer-portal-configure.md)或[PowerShell](availability-group-listener-powershell-configure.md)配置负载均衡器。


## <a name="next-steps"></a>后续步骤

还可以使用 [Az SQL VM CLI](availability-group-az-cli-configure.md) 配置可用性组。 


