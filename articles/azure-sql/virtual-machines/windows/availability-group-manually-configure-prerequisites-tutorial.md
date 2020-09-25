---
title: 教程：可用性组先决条件
description: 本教程展示了如何配置在 Azure 虚拟机中创建 SQL Server AlwaysOn 可用性组的先决条件。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 278e5feb327c1376b7644050f414f680334d5c50
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91263226"
---
# <a name="prerequisites-for-creating-always-on-availability-groups-on-sql-server-on-azure-virtual-machines"></a>在 Azure 虚拟机中的 SQL Server 上创建 Always On 可用性组的先决条件

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本教程展示了如何完成[在 Azure 虚拟机 (VM) 中创建 SQL Server AlwaysOn 可用性组](availability-group-manually-configure-tutorial.md)的先决条件。 完成先决条件后，你将在单个资源组中拥有一台域控制器、两台 SQL Server VM 和一台见证服务器。

**时间估计**：可能需要几个小时才能完成必备组件配置。 大部分时间花费在创建虚拟机上。

下图演示了在本教程中构建的内容。

![可用性组](./media/availability-group-manually-configure-prerequisites-tutorial-/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>查看可用性组文档

本教程假设你已基本了解 SQL Server AlwaysOn 可用性组。 若不熟悉此技术，请参阅 [AlwaysOn 可用性组概述 (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx)。


## <a name="create-an-azure-account"></a>创建 Azure 帐户

需要一个 Azure 帐户。 可[建立免费的 Azure 帐户](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic)或[激活 Visual Studio 订阅者权益](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits)。

## <a name="create-a-resource-group"></a>创建资源组

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在门户中选择 **+** 以创建一个新对象。

   ![新建对象](./media/availability-group-manually-configure-prerequisites-tutorial-/01-portalplus.png)

3. 在“市场”搜索窗口中键入“资源组”。 

   ![资源组](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroupsymbol.png)

4. 选择“资源组”。
5. 选择“创建” 。
6. 在“资源组名称”下，键入资源组的名称。 例如，键入 **sql-ha-rg**。
7. 若有多个 Azure 订阅，请验证该订阅是否为要在其中创建可用性组的 Azure 订阅。
8. 选择一个位置。 该位置为要在其中创建可用性组的 Azure 区域。 本文将在一个 Azure 位置生成所有资源。
9. 验证是否已选中“固定到仪表板”。 此可选设置将在 Azure 门户仪表板上放置资源组的快捷方式。

   ![资源组](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroup.png)

10. 选择“创建”以创建资源组。

Azure 会创建资源组，并在门户中固定资源组的快捷方式。

## <a name="create-the-network-and-subnets"></a>创建网络和子网

下一步是在 Azure 资源组中创建网络和子网。

此解决方案使用一个包含两个子网的虚拟网络。 有关 Azure 中网络的详细信息，请参阅[虚拟网络概述](../../../virtual-network/virtual-networks-overview.md)。

若要在 Azure 门户中创建虚拟网络，请执行以下操作：

1. 在资源组中，选择“+ 添加”。 

   ![新建项](./media/availability-group-manually-configure-prerequisites-tutorial-/02-newiteminrg.png)
2. 搜索“虚拟网络”。

     ![搜索虚拟网络](./media/availability-group-manually-configure-prerequisites-tutorial-/04-findvirtualnetwork.png)
3. 选择“虚拟网络”。
4. 在“虚拟网络”中，选择“资源管理器”部署模型，然后选择“创建”。

    下表显示了虚拟网络的设置：

   | **字段** | Value |
   | --- | --- |
   | **名称** |autoHAVNET |
   | **地址空间** |10.33.0.0/24 |
   | **子网名称** |管理员 |
   | **子网地址范围** |10.33.0.0/29 |
   | **订阅** |指定要使用的订阅。 如果只有一个订阅，“订阅”字段将是空白的。 |
   | **资源组** |选择“使用现有项”，然后选择资源组的名称。 |
   | **位置** |指定 Azure 位置。 |

   地址空间和子网地址范围可能与此表中有所不同。 门户根据具体的订阅建议可用的地址空间和相应的子网地址范围。 如果地址空间不足，请使用其他订阅。

   本示例使用子网名称 **Admin**。此子网用于域控制器。

5. 选择“创建” 。

   ![配置虚拟网络](./media/availability-group-manually-configure-prerequisites-tutorial-/06-configurevirtualnetwork.png)

Azure 返回到门户仪表板，并在创建好新网络时发出通知。

### <a name="create-a-second-subnet"></a>创建第二个子网。

新虚拟网络包含一个名为 **Admin**的子网。域控制器使用此子网。 SQL Server VM 使用名为 **SQL** 的另一个子网。 若要配置此子网，请执行以下操作：

1. 在仪表板上，选择你创建的资源组 **SQL-HA-RG**。 在“资源”下的资源组中找到网络。

    如果看不到 **SQL-HA-RG**，请选择“资源组”并根据资源组名称进行筛选，这样就可以找到它。

2. 选择资源列表中的 **autoHAVNET**。 
3. 在“autoHAVNET”虚拟网络中的“设置”下，选择“子网”。

    请记下已创建的子网。

   ![配置虚拟网络](./media/availability-group-manually-configure-prerequisites-tutorial-/07-addsubnet.png)

5. 若要创建第二个子网，请选择“+ 子网”。
6. 在“添加子网”中，通过在“名称”下键入 **sqlsubnet** 配置子网。 Azure 自动指定一个有效的**地址范围**。 请确认此地址范围中至少有 10 个地址。 生产环境中可能需要更多地址。
7. 选择“确定” 。

    ![配置虚拟网络](./media/availability-group-manually-configure-prerequisites-tutorial-/08-configuresubnet.png)

下表汇总了网络配置设置：

| **字段** | Value |
| --- | --- |
| **名称** |**autoHAVNET** |
| **地址空间** |此值取决于订阅中可用的地址空间。 典型值为 10.0.0.0/16。 |
| **子网名称** |**admin** |
| **子网地址范围** |此值取决于订阅中可用的地址范围。 典型值为 10.0.0.0/24。 |
| **子网名称** |**sqlsubnet** |
| **子网地址范围** |此值取决于订阅中可用的地址范围。 典型值为 10.0.1.0/24。 |
| **订阅** |指定要使用的订阅。 |
| **资源组** |**SQL-HA-RG** |
| **位置** |指定为资源组选择的同一位置。 |

## <a name="create-availability-sets"></a>创建可用性集

创建虚拟机前，需创建可用性集。 可用性集可减少计划内或计划外维护事件的停机时间。 Azure 可用性集是 Azure 置于物理容错域和更新域上的逻辑资源组。 容错域可确保可用性集的成员具有单独的电源和网络资源。 更新域确保可用性集的成员不会同时停机进行维护。 有关详细信息，请参阅[管理虚拟机的可用性](../../../virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

需要两个可用性集。 一个用于域控制器。 另一个用于 SQL Server VM。

若要创建可用性集，请转到资源组并选择“添加”。 通过键入“可用性集”筛选结果。 选择结果中的“可用性集”，然后选择“创建”。

根据下表中的参数配置两个可用性集：

| **字段** | 域控制器可用性集 | SQL Server 可用性集 |
| --- | --- | --- |
| **名称** |adavailabilityset |sqlavailabilityset |
| **资源组** |sql-ha-rg |sql-ha-rg |
| **容错域** |3 |3 |
| **更新域** |5 |3 |

创建可用性集之后，请返回到 Azure 门户中的资源组。

## <a name="create-domain-controllers"></a>创建域控制器

创建网络、子网、可用性集后，即可为域控制器创建虚拟机。

### <a name="create-virtual-machines-for-the-domain-controllers"></a>为域控制器创建虚拟机

若要创建并配置域控制器，请返回到 **SQL-HA-RG** 资源组。

1. 选择“添加”  。 
2. 键入 **Windows Server 2016 Datacenter**。
3. 选择“Windows Server 2016 Datacenter”。 在“Windows Server 2016 Datacenter”中，确认部署模型为“资源管理器”，然后选择“创建”。 

重复上述步骤创建两个虚拟机。 将两个虚拟机命名为：

* ad-primary-dc
* ad-secondary-dc

  > [!NOTE]
  > **ad-secondary-dc** 虚拟机是可选的，用于为 Active Directory 域服务提供高可用性。
  >

下表显示了这两个虚拟机的设置：

| **字段** | Value |
| --- | --- |
| **名称** |第一个域控制器：*ad-primary-dc*。</br>第二个域控制器 *ad-secondary-dc*。 |
| **VM 磁盘类型** |SSD |
| **用户名** |DomainAdmin |
| **密码** |Contoso!0000 |
| **订阅** |*订阅* |
| **资源组** |sql-ha-rg |
| **位置** |*你的位置* |
| **大小** |DS1_V2 |
| **存储** | **使用托管磁盘** - **是** |
| **虚拟网络** |autoHAVNET |
| **子网** |管理员 |
| **公共 IP 地址** |*与 VM 同名* |
| **网络安全组** |*与 VM 同名* |
| **可用性集** |adavailabilityset </br>**容错域**:2 </br>**更新域**:2|
| **诊断** |已启用 |
| **诊断存储帐户** |*自动创建* |

   >[!IMPORTANT]
   >只能在创建 VM 时将 VM 放入可用性集。 创建 VM 后，无法更改可用性集。 请参阅[管理虚拟机的可用性](../../../virtual-machines/linux/manage-availability.md)。

Azure 会创建虚拟机。

创建虚拟机后，请配置域控制器。

### <a name="configure-the-domain-controller"></a>配置域控制器

执行以下步骤，将 **ad-primary-dc** 计算机配置为 corp.contoso.com 的域控制器。

1. 在门户中打开 **SQL-HA-RG** 资源组，然后选择 **ad-primary-dc** 计算机。 在“ad-primary-dc”中，选择“连接”，打开用于远程桌面访问的 RDP 文件。 

    ![连接到虚拟机](./media/availability-group-manually-configure-prerequisites-tutorial-/20-connectrdp.png)

2. 使用已配置的管理员帐户 ( **\DomainAdmin**) 和密码 (**Contoso!0000**) 登录。
3. 默认情况下，应显示“服务器管理器”仪表板。
4. 选择仪表板上的“添加角色和功能”链接。

    ![服务器管理器 - 添加角色](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)

5. 选择“下一步”，直到到达“服务器角色”部分。 
6. 选择“Active Directory 域服务”和“DNS 服务器”角色。  出现提示时，添加这些角色所需的任何其他功能。

   > [!NOTE]
   > Windows 会警告你没有静态 IP 地址。 若要测试配置，请选择“继续”。 对于生产方案，请在 Azure 门户中将 IP 地址设置为静态，或[使用 PowerShell 设置域控制器计算机的静态 IP 地址](../../../virtual-network/virtual-networks-reserved-private-ip.md)。
   >

    ![“添加角色”对话框](./media/availability-group-manually-configure-prerequisites-tutorial-/23-addroles.png)

7. 选择“下一步”，直到显示“确认”部分。  选中“必要时自动重启目标服务器”复选框。
8. 选择“安装”。
9. 功能安装完毕后，返回到“服务器管理器”仪表板。
10. 选择左侧窗格中的新“AD DS”选项。
11. 选择黄色警告栏上的“更多”链接。

    ![DNS 服务器 VM 上的 AD DS 对话框](./media/availability-group-manually-configure-prerequisites-tutorial-/24-addsmore.png)
    
12. 在“所有服务器任务详细信息”对话框的“操作”栏中，选择“将此服务器提升为域控制器”。  
13. 在“Active Directory 域服务配置向导”中，使用以下值：

    | **Page** | 设置 |
    | --- | --- |
    | **部署配置** |**添加新林**<br/> **根域名** = corp.contoso.com |
    | **域控制器选项** |**DSRM 密码** = Contoso!0000<br/>**确认密码** = Contoso!0000 |

14. 选择“下一步”以浏览向导中的其他页。 在“必备项检查”页上，确认看到以下消息：“所有先决条件检查都成功通过”。 查看任何适用的警告消息，但可继续安装。
15. 选择“安装”。 **ad-primary-dc** 虚拟机自动重新启动。

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>记下主域控制器的 IP 地址

为 DNS 使用主域控制器。 记下主域控制器的 IP 地址。

获取主域控制器 IP 地址的方法之一是使用 Azure 门户。

1. 在 Azure 门户中打开资源组。

2. 选择主域控制器。

3. 在主域控制器中，选择“网络接口”。

![网络接口](./media/availability-group-manually-configure-prerequisites-tutorial-/25-primarydcip.png)

记下此服务器的专用 IP 地址。

### <a name="configure-the-virtual-network-dns"></a>配置虚拟网络 DNS

在创建第一个域控制器并在第一台服务器上启用 DNS 后，将虚拟网络配置为将此服务器用作 DNS。

1. 在 Azure 门户中，选择虚拟网络。

2. 在“设置”下，选择“DNS 服务器”。 

3. 选择“自定义”，键入主域控制器的专用 IP 地址。

4. 选择“保存” 。

### <a name="configure-the-second-domain-controller"></a>配置第二个域控制器

在主域控制器重新启动之后，可以配置第二个域控制器。 此可选步骤适用于实现高可用性。 遵循以下步骤配置第二个域控制器：

1. 在门户中打开 **SQL-HA-RG** 资源组，然后选择 **ad-secondary-dc** 计算机。 在“ad-secondary-dc”中，选择“连接”，打开用于远程桌面访问的 RDP 文件。 
2. 使用已配置的管理员帐户 (**BUILTIN\DomainAdmin**) 和密码 (**Contoso!0000**) 登录到 VM。
3. 将首选 DNS 服务器地址更改为域控制器的地址。
4. 在“网络和共享中心”中，选择网络接口。

   ![Linux](./media/availability-group-manually-configure-prerequisites-tutorial-/26-networkinterface.png)

5. 选择“属性”。
6. 选择“Internet 协议版本 4 (TCP/IPv4)”，然后选择“属性”。
7. 选择“使用以下 DNS 服务器地址”，然后在“首选 DNS 服务器”中指定主域控制器的地址。
8. 选择“确定”，然后选择“关闭”以提交更改。  现在能够将该 VM 加入到 **corp.contoso.com**中。

   >[!IMPORTANT]
   >如果在更改 DNS 设置后与远程桌面断开了连接，请转到 Azure 门户并重新启动虚拟机。

9. 通过远程桌面连接到辅助域控制器，打开“服务器管理器仪表板”。
10. 选择仪表板上的“添加角色和功能”链接。

    ![服务器管理器 - 添加角色](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)
11. 选择“下一步”，直到到达“服务器角色”部分。 
12. 选择“Active Directory 域服务”和“DNS 服务器”角色。  出现提示时，添加这些角色所需的任何其他功能。
13. 功能安装完毕后，返回到“服务器管理器”仪表板。
14. 选择左侧窗格中的新“AD DS”选项。
15. 选择黄色警告栏上的“更多”链接。
16. 在“所有服务器任务详细信息”对话框的“操作”栏中，选择“将此服务器提升为域控制器”。  
17. 在“部署配置”下面，选择“将域控制器添加到现有域”。 

    ![部署配置](./media/availability-group-manually-configure-prerequisites-tutorial-/28-deploymentconfig.png)

18. 单击“选择”。
19. 使用管理员帐户 (**CORP.CONTOSO.COM\domainadmin**) 和密码 (**Contoso!0000**) 进行连接。
20. 在“从林中选择域”中，选择你的域，然后选择“确定”。
21. 在“域控制器选项”中，使用默认值并设置 DSRM 密码。

    >[!NOTE]
    >“DNS 选项”页可能会警告你无法创建此 DNS 服务器的委托。 在非生产环境中可以忽略此警告。
    >

22. 选择“下一步”，直到出现“先决条件检查”对话框。  然后选择“安装”。

在服务器完成配置更改后，重启服务器。

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>将第二个域控制器的专用 IP 地址添加到 VPN DNS 服务器中

在 Azure 门户中，在虚拟网络下更改 DNS 服务器，以包含辅助域控制器的 IP 地址。 此设置可实现 DNS 服务冗余。

### <a name="configure-the-domain-accounts"></a><a name="DomainAccounts"></a>配置域帐户

后续步骤将配置 Active Directory 帐户。 下表显示了帐户：

| |安装帐户<br/> |sqlserver-0 <br/>SQL Server 和 SQL 代理服务帐户 |sqlserver-1<br/>SQL Server 和 SQL 代理服务帐户
| --- | --- | --- | ---
|**名字** |安装 |SQLSvc1 | SQLSvc2
|**用户 SamAccountName** |安装 |SQLSvc1 | SQLSvc2

使用以下步骤创建每个帐户。

1. 登录到 **ad-primary-dc** 计算机。
2. 在“服务器管理器”中，选择“工具”，然后选择“Active Directory 管理中心”。     
3. 在左窗格中选择“corp (local)”。
4. 在右侧的“任务”窗格中，选择“新建”，然后选择“用户”。  

   ![Active Directory 管理中心](./media/availability-group-manually-configure-prerequisites-tutorial-/29-addcnewuser.png)

   >[!TIP]
   >为每个帐户设置复杂密码。<br/> 对于非生产环境，请将用户帐户设置为永不过期。
   >

5. 选择“确定”以创建用户。
6. 针对每个帐户（共三个）重复上述步骤。

### <a name="grant-the-required-permissions-to-the-installation-account"></a>向安装帐户授予所需的权限

1. 在“Active Directory 管理中心”的左窗格中，选择“corp (本地)”。  然后，在右侧的“任务”窗格中，选择“属性”。 

    ![CORP 用户属性](./media/availability-group-manually-configure-prerequisites-tutorial-/31-addcproperties.png)

2. 选择“扩展”，然后选择“安全性”选项卡上的“高级”按钮。  
3. 在“corp 的高级安全设置”对话框中，选择“添加”。
4. 单击“选择主体”，搜索“CORP\Install”，然后选择“确定”。
5. 选中“读取所有属性”复选框。

6. 选中“创建计算机对象”复选框。

     ![Corp 用户权限](./media/availability-group-manually-configure-prerequisites-tutorial-/33-addpermissions.png)

7. 选择“确定”，然后再选择“确定”。  关闭“corp”属性窗口。

现已完成 Active Directory 和用户对象的配置，接下来请创建两台 SQL Server VM 和一台见证服务器 VM。 然后将这三个 VM 加入域。

## <a name="create-sql-server-vms"></a>创建 SQL Server VM

再创建三个虚拟机。 该解决方案需要两个具有 SQL Server 实例的虚拟机。 第三个虚拟机充当见证服务器。 Windows Server 2016 可以使用[云见证](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)。 但是为了与旧版操作系统保持一致，本文使用虚拟机作为见证服务器。  

在继续操作之前，请考虑以下设计决策。

* **存储：Azure 托管磁盘**

   将 Azure 托管磁盘用作虚拟机存储。 Microsoft 建议为 SQL Server 虚拟机使用托管磁盘。 托管磁盘在后台处理存储。 此外，当使用托管磁盘的虚拟机位于同一可用性集中时，Azure 会分发存储资源以提供适当冗余。 有关其他信息，请参阅 [Azure 托管磁盘概述](../../../virtual-machines/managed-disks-overview.md)。 有关可用性集中托管磁盘的具体信息，请参阅[为可用性集中的 VM 使用托管磁盘](../../../virtual-machines/linux/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)。

* **网络：生产环境中的专用 IP 地址**

   本教程为虚拟机使用公共 IP 地址。 使用公共 IP 地址可以通过 Internet 直接远程连接到虚拟机，从而使配置过程更加轻松。 在生产环境中，Microsoft 建议仅使用专用 IP 地址，以减少 SQL Server 实例 VM 资源的漏洞涉及面。

### <a name="create-and-configure-the-sql-server-vms"></a>创建并配置 SQL Server VM

接下来，创建三台 VM - 两台 SQL Server VM 和一台用于其他群集节点的 VM。 若要创建每台 VM，请返回到“SQL-HA-RG”资源组，然后选择“添加”。 搜索相应的库项，选择“虚拟机”，然后选择“从库中”。 参考下表中的信息创建 VM：


| Page | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| 选择相应的库项 |**Windows Server 2016 Datacenter** |**Windows Server 2016 上的 SQL Server 2016 SP1 Enterprise** |**Windows Server 2016 上的 SQL Server 2016 SP1 Enterprise** |
| 虚拟机配置**基本信息** |**名称** = cluster-fsw<br/>**用户名** = DomainAdmin<br/>**密码** = Contoso!0000<br/>**订阅** = 自己的订阅<br/>**资源组** = SQL-HA-RG<br/>**位置** = 你的 Azure 位置 |**名称** = sqlserver-0<br/>**用户名** = DomainAdmin<br/>**密码** = Contoso!0000<br/>**订阅** = 自己的订阅<br/>**资源组** = SQL-HA-RG<br/>**位置** = 你的 Azure 位置 |**名称** = sqlserver-1<br/>**用户名** = DomainAdmin<br/>**密码** = Contoso!0000<br/>**订阅** = 自己的订阅<br/>**资源组** = SQL-HA-RG<br/>**位置** = 你的 Azure 位置 |
| 虚拟机配置**大小** |大小 = DS1\_V2（1 个 vCPU、3.5GB） |大小 = DS2\_V2（2 个 vCPU、7GB）</br>大小必须支持 SSD 存储（高级磁盘支持。 )) |大小 = DS2\_V2（2 个 vCPU、7GB） |
| 虚拟机配置**设置** |**存储**：使用托管磁盘。<br/>**虚拟网络** = autoHAVNET<br/>**子网** = sqlsubnet(10.1.1.0/24)<br/>**公共 IP 地址**自动生成。<br/>**网络安全组** = 无<br/>**监视诊断** = 已启用<br/>**诊断存储帐户** = 使用自动生成的存储帐户<br/>**可用性集** = sqlAvailabilitySet<br/> |**存储**：使用托管磁盘。<br/>**虚拟网络** = autoHAVNET<br/>**子网** = sqlsubnet(10.1.1.0/24)<br/>**公共 IP 地址**自动生成。<br/>**网络安全组** = 无<br/>**监视诊断** = 已启用<br/>**诊断存储帐户** = 使用自动生成的存储帐户<br/>**可用性集** = sqlAvailabilitySet<br/> |**存储**：使用托管磁盘。<br/>**虚拟网络** = autoHAVNET<br/>**子网** = sqlsubnet(10.1.1.0/24)<br/>**公共 IP 地址**自动生成。<br/>**网络安全组** = 无<br/>**监视诊断** = 已启用<br/>**诊断存储帐户** = 使用自动生成的存储帐户<br/>**可用性集** = sqlAvailabilitySet<br/> |
| 虚拟机配置 **SQL Server 设置** |不适用 |**SQL 连接** = 专用（虚拟网络内部）<br/>**端口** = 1433<br/>**SQL 身份验证** = 禁用<br/>**存储配置** = 常规<br/>**自动修补** = 星期日 2:00<br/>**自动备份** = 已禁用</br>**Azure 密钥保管库集成** = 已禁用 |**SQL 连接** = 专用（虚拟网络内部）<br/>**端口** = 1433<br/>**SQL 身份验证** = 禁用<br/>**存储配置** = 常规<br/>**自动修补** = 星期日 2:00<br/>**自动备份** = 已禁用</br>**Azure 密钥保管库集成** = 已禁用 |

<br/>

> [!NOTE]
> 此处建议的计算机大小适合用于在 Azure 虚拟机中测试可用性组。 为获得生产工作负荷的最佳性能，请参阅 [Azure 虚拟机中 SQL Server 的性能最佳实践](performance-guidelines-best-practices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中关于 SQL Server 计算机大小和配置的建议。
>

预配完三台 VM 后，需将其加入到 **corp.contoso.com** 域中，并向这些计算机授予 CORP\Install 管理权限。

### <a name="join-the-servers-to-the-domain"></a><a name="joinDomain"></a>将服务器加入域

现可将这些 VM 加入 **corp.contoso.com**。 针对 SQL Server VM 和文件共享见证服务器执行以下步骤：

1. 使用 **BUILTIN\DomainAdmin** 远程连接到虚拟机。
2. 在“服务器管理器”中，选择“本地服务器”。
3. 选择“工作组”链接。
4. 在“计算机名”部分中，选择“更改”。 
5. 选中“域”复选框并在文本框中键入 **corp.contoso.com**。 选择“确定” 。
6. 在“Windows 安全性”弹出对话框中，指定默认域管理员帐户 (**CORP\DomainAdmin**) 和密码 (**Contoso!0000**) 的凭据。
7. 在看到“欢迎使用 corp.contoso.com 域”消息时，选择“确定”。
8. 选择“关闭”，然后选择弹出对话框中的“立即重启”。 

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>将 Corp\Install 用户添加为每个群集 VM 上的管理员

将每个虚拟机作为域的成员重新启动后，请将 **CORP\Install** 添加为本地管理员组的成员。

1. 等待 VM 重启，然后从主域控制器重新启动 RDP 文件，以使用 CORP\DomainAdmin 帐户登录到 sqlserver-0。

   >[!TIP]
   >请确保使用域管理员帐户登录。 在前面的步骤中，使用的是内置管理员帐户。 将服务器加入域后，便可以使用域帐户。 在 RDP 会话中，指定*域*\\*用户名*。
   >

2. 在“服务器管理器”中，选择“工具”，然后选择“计算机管理”。  
3. 在“计算机管理”窗口中，展开“本地用户和组”，并选择“组”。  
4. 双击“管理员”组。
5. 在“管理员属性”对话框中，选择“添加”按钮。 
6. 输入用户 **CORP\Install**，然后选择“确定”。
7. 选择“确定”以关闭“管理员属性”对话框。 
8. 在 sqlserver-1 和 cluster-fsw 上重复上述步骤。

### <a name="set-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>设置 SQL Server 服务帐户

在每个 SQL Server VM 上设置 SQL Server 服务帐户。 使用配置域帐户时创建的帐户。

1. 打开“SQL Server 配置管理器”。
2. 右键单击 SQL Server 服务，然后选择“属性”。
3. 设置帐户和密码。
4. 在另一个 SQL Server VM 上重复上述步骤。  

对于 SQL Server 可用性组，每个 SQL Server VM 都需要以域帐户的身份运行。

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>在每个 SQL Server VM 上创建安装帐户的登录名

使用安装帐户 (CORP\install) 配置可用性组。 此帐户需是每个 SQL Server VM 上的 **sysadmin** 固定服务器角色的成员。 以下步骤将创建安装帐户的登录名：

1. 使用 \<MachineName\>\DomainAdmin 帐户通过远程桌面协议 (RDP) 连接到服务器。

1. 打开 SQL Server Management Studio，并连接到 SQL Server 的本地实例。

1. 在“对象资源管理器”中，选择“安全性”。 

1. 右键单击“登录名”。 选择“新建登录名”。

1. 在“登录名 - 新建”中，选择“搜索”。 

1. 选择“位置”。

1. 输入域管理员的网络凭据。

1. 使用安装帐户。

1. 将该登录名设置为 **sysadmin** 固定服务器角色的成员。

1. 选择“确定” 。

在另一个 SQL Server VM 上重复上述步骤。

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>在两个 SQL Server VM 上添加故障转移群集功能

若要添加故障转移群集功能，请在两个 SQL Server VM 上执行以下步骤：

1. 使用 *CORP\install* 帐户通过远程桌面协议 (RDP) 连接到 SQL Server 虚拟机。 打开“服务器管理器仪表板”。
2. 选择仪表板上的“添加角色和功能”链接。

    ![服务器管理器 - 添加角色](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)

3. 选择“下一步”，直到看到“服务器功能”部分。 
4. 在“功能”中，选择“故障转移群集”。 
5. 添加其他所有必备功能。
6. 选择“安装”以添加功能。

在另一个 SQL Server VM 上重复上述步骤。

  >[!NOTE]
  > 现在，此步骤以及实际将 SQL Server VM 加入故障转移群集的操作可通过 [Azure SQL VM CLI](availability-group-az-cli-configure.md) 和 [Azure 快速启动模板](availability-group-quickstart-template-configure.md)自动执行。
  >


## <a name="configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"></a>在每个 SQL Server VM 上配置防火墙

该解决方案要求在防火墙中打开以下 TCP 端口：

- **SQL Server VM**：端口 1433，用于 SQL Server 的默认实例。
- **Azure 负载均衡器探测器：** 任何可用端口。 示例中经常使用 59999。
- **数据库镜像终结点：** 任何可用端口。 示例中经常使用 5022。

需要在两个 SQL Server VM 上都打开这些防火墙端口。

端口的打开方式取决于所用的防火墙解决方案。 下一部分将介绍如何在 Windows 防火墙中打开端口。 在每个 SQL Server VM 上打开所需的端口。

### <a name="open-a-tcp-port-in-the-firewall"></a>在防火墙中打开 TCP 端口

1. 在第一个 SQL Server 的“开始”屏幕中，启动“高级安全 Windows 防火墙”。 
2. 在左窗格中，选择“入站规则”。 在右窗格上，选择“新建规则”。
3. 对于“规则类型”，请选择“端口”。 
4. 对于端口，请指定“TCP”，并键入相应的端口号。 请参阅以下示例：

   ![SQL 防火墙](./media/availability-group-manually-configure-prerequisites-tutorial-/35-tcpports.png)

5. 选择“**下一步**”。
6. 在“操作”页上，保持选中“允许连接”，然后选择“下一步”。
7. 在“配置文件”页上，接受默认设置，然后选择“下一步”。
8. 在“名称”页的“名称”文本框中指定一个规则名称（如 Azure LB Probe），然后选择“完成”。

在另一个 SQL Server VM 上重复上述步骤。

## <a name="configure-system-account-permissions"></a>配置系统帐户权限

若要创建系统帐户并授予相应的权限，请在每个 SQL Server 实例上完成以下步骤：

1. 在每个 SQL Server 实例上为 `[NT AUTHORITY\SYSTEM]` 创建一个帐户。 以下脚本将创建此帐户：

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. 在每个 SQL Server 实例上向 `[NT AUTHORITY\SYSTEM]` 授予以下权限：

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   以下脚本将授予这些权限：

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>后续步骤

* [在 Azure 虚拟机上创建 SQL Server AlwaysOn 可用性组](availability-group-manually-configure-tutorial.md)
