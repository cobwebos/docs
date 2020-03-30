---
title: 配置可用性组（Azure 快速入门模板）
description: 使用 Azure 快速启动模板创建 Windows 故障转移群集、将 SQL Server VM 加入群集、创建侦听器以及配置 Azure 中的内部负载均衡器。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: edf810dfc975eebaf261eac7b89106c9e29c759c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022386"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-an-azure-vm"></a>使用 Azure 快速入门模板在 Azure VM 上配置 SQL Server 的可用性组
本文介绍如何使用 Azure 快速入门模板在 Azure 中为 SQL Server 虚拟机部分自动化 SQL Server 虚拟机的可用性组配置的部署。 此过程使用了两个 Azure 快速入门模板： 

   | 模板 | 描述 |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | 创建 Windows 故障转移群集，并将 SQL Server VM 加入到该群集中。 |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | 创建可用性组侦听器并配置内部负载均衡器。 仅当使用**101 sql-vm-ag 设置**模板创建 Windows 故障转移群集时，才能使用此模板。 |
   | &nbsp; | &nbsp; |

可用性组配置的其他部分必须手动完成，例如创建可用性组和创建内部负载均衡器。 本文提供自动和手动步骤的顺序。
 

## <a name="prerequisites"></a>先决条件 
要使用快速入门模板自动设置"始终打开"可用性组，您必须具备以下先决条件： 
- [Azure 订阅](https://azure.microsoft.com/free/)。
- 一个具有域控制器的资源组。 
- 运行 SQL Server [2016（或更高版本）的 Azure 中](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)一个或多个域联接的 VM 位于相同的可用性集或可用性区域中，并已[注册到 SQL VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)。  
- 两个可用（任何实体不使用）IP 地址：一个用于内部负载均衡器，另一个用于可用性组同一子网中的可用性组侦听器。 如果使用现有负载均衡器，则只需要一个可用的 IP 地址。  

## <a name="permissions"></a>权限
使用 Azure 快速入门模板配置"始终打开的可用性"组需要以下权限： 

- 在域中具有 **"创建计算机对象"** 权限的现有域用户帐户。  例如，域管理员帐户通常具有足够的权限（例如： account@domain.com __ 该帐户还应该属于每个 VM 上负责创建群集的本地管理员组。
- 控制 SQL Server 服务的域用户帐户。 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>第 1 步：使用快速入门模板创建故障转移群集并将 SQL Server VM 加入群集 
在 SQL Server VM 资源提供程序注册 SQL Server VM 后，可以将 SQL Server VM 加入到*SqlVirtualMachine 组*。 此资源定义 Windows 故障转移群集的元数据。 元数据包括版本、版本、完全限定的域名、用于管理群集和 SQL Server 服务的 Active Directory 帐户以及作为云见证的存储帐户。 

将 SQL Server VM 添加到 *SqlVirtualMachineGroups* 资源组会启动 Windows 故障转移群集服务，以便创建群集并将这些 SQL Server VM 加入该群集。 此步骤通过**101 sql-vm-ag 设置**快速入门模板实现自动化。 您可以使用以下步骤实现它：

1. 转到[**101 sql-vm-ag 设置**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup)快速入门模板。 然后，选择 **"部署到 Azure"** 以在 Azure 门户中打开快速入门模板。
1. 填写所需的字段以配置 Windows 故障转移群集的元数据。 您可以将可选字段留空。

   下表显示了模板的必要值： 

   | **字段** | “值” |
   | --- | --- |
   | **订阅** |  SQL Server VM 所在的订阅。 |
   |**资源组** | SQL Server VM 所在的资源组。 | 
   |**故障转移群集名称** | 新 Windows 故障转移群集所需的名称。 |
   | **现有 VM 列表** | 要参与可用性组的 SQL Server VM，并将成为此新群集的一部分。 用逗号和空格分隔这些值（例如 *：SQLVM1、SQLVM2*）。 |
   | **SQL 服务器版本** | SQL Server VM 的 SQL Server 版本。 从下拉列表中选择它。 目前，仅支持 SQL Server 2016 和 SQL Server 2017 映像。 |
   | **现有的完全限定域名** | SQL Server VM 所在的域的现有 FQDN。 |
   | **现有域帐户** | 在模板部署期间创建[CNO](/windows-server/failover-clustering/prestage-cluster-adds)时，在域中具有 **"创建计算机对象"** 权限的现有域用户帐户。 例如，域管理员帐户通常具有足够的权限（例如： account@domain.com ** 该帐户还应该属于每个 VM 上负责创建群集的本地管理员组。| 
   | **域帐户密码** | 上述域用户帐户的密码。 | 
   | **现有 SQL 服务帐户** | 在可用性组部署期间控制[SQL Server 服务的](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions)域用户帐户（例如： account@domain.com。 |
   | **SQL 服务密码** | 用于控制 SQL Server 服务的域用户帐户使用的密码。 |
   | **云见证名称** | 将创建和用于云见证的新 Azure 存储帐户。 您可以修改此名称。 |
   | **\_工件位置** | 此字段默认设置，不应修改。 |
   | **\_工件位置 Sas 令牌** | 此字段有意留空。 |
   | &nbsp; | &nbsp; |

1. 如果您同意条款和条件，请选择 **"我同意上述条款和条件"** 复选框。 然后选择 **"购买"** 以完成快速入门模板的部署。 
1. 要监视部署，请从顶部导航横幅中的 **"通知**"铃图标中选择部署，或者转到 Azure 门户中的**资源组**。 选择 **"设置"** 下的 **"部署**"，然后选择**Microsoft.模板**部署。 

>[!NOTE]
> 模板部署期间提供的凭据仅存储部署长度。 部署完成后，这些密码将被删除。 如果向群集添加更多 SQL Server VM，系统将要求您再次提供它们。 


## <a name="step-2-manually-create-the-availability-group"></a>第 2 步：手动创建可用性组 
通过使用[SQL 服务器管理工作室](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)[、PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)或[Transact-SQL，](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)按照通常方式手动创建可用性组。 

>[!IMPORTANT]
> 此时*不要*创建侦听器，因为**101-sql-vm-aglistener 设置**快速入门模板在步骤 4 中会自动执行此操作。 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>第 3 步：手动创建内部负载均衡器
始终打开可用性组侦听器需要 Azure 负载均衡器的内部实例。 内部负载均衡器为可用性组侦听器提供"浮动"IP 地址，以便更快地进行故障转移和重新连接。 如果可用性组中的 SQL Server VM 属于同一可用性集的一部分，则可以使用基本负载均衡器。 否则，您需要使用标准负载均衡器。 

> [!IMPORTANT]
> 内部负载均衡器应与 SQL Server VM 实例位于同一虚拟网络中。 

您只需创建内部负载均衡器。 在步骤 4 中 **，101-sql-vm-aglistener 设置**快速启动模板处理配置的其余部分（如后端池、运行状况探测和负载平衡规则）。 

1. 在 Azure 门户中，打开包含 SQL Server 虚拟机的资源组。 
2. 在资源组中，选择 **"添加**"。
3. 搜索“负载均衡器”。**** 在搜索结果中，选择**由 Microsoft**发布的**负载均衡器**。
4. 在 **"负载均衡器"** 边栏选项卡上，选择 **"创建**"。
5. 在“创建负载均衡器”对话框中配置负载均衡器，如下所示：****

   | 设置 | “值” |
   | --- | --- |
   | **名称** |输入表示负载均衡器的文本名称。 例如，输入**sqlLB**。 |
   | **类型** |**内部**：大多数实施方案使用内部负载均衡器，它允许应用程序相同的虚拟网络中连接到可用性组。  </br> **外部**：允许应用程序通过公共互联网连接连接到可用性组。 |
   | **虚拟网络** | 选择 SQL Server 实例所在的虚拟网络。 |
   | **子** | 选择 SQL Server 实例所在的子网。 |
   | **IP 地址分配** |**静态** |
   | **专用 IP 地址** | 指定子网中的某个可用 IP 地址。 |
   | **订阅** |如果有多个订阅，可能会显示此字段。 选择要与此资源关联的订阅。 它通常与可用性组的所有资源相同的订阅。 |
   | **资源组** |选择 SQL Server 实例所在的资源组。 |
   | **位置** |选择 SQL Server 实例所在的 Azure 位置。 |
   | &nbsp; | &nbsp; |

6. 选择 **“创建”**。 


>[!IMPORTANT]
> 每个 SQL Server VM 的公共 IP 资源应具有与标准负载均衡器兼容的标准 SKU。 要确定 VM 公共 IP 资源的 SKU，请转到**资源组**，为 SQL Server VM 选择**公共 IP 地址**资源，并在 **"概述"** 窗格中查找**SKU**下的值。 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>步骤 4：使用快速入门模板创建可用性组侦听器并配置内部负载均衡器

使用**101-sql-vm-aglistener 设置**快速启动模板创建可用性组侦听器并自动配置内部负载均衡器。 该模板提供 Microsoft.SqlVirtualMachine/Sql虚拟计算机组/可用性组侦听器资源。 **101-sql-vm-aglistener-setup** 快速入门模板通过 SQL VM 资源提供程序执行以下操作：

- 为侦听器创建新的前端 IP 资源（基于在部署过程中提供的 IP 地址值）。 
- 配置群集和内部负载均衡器的网络设置。 
- 为内部负载平衡器、运行状况探测和负载平衡规则配置后端池。
- 使用给定的 IP 地址和名称创建可用性组侦听器。
 
>[!NOTE]
> 仅当使用 101 sql-vm-ag 设置模板创建 Windows 故障转移群集时，才能使用**101-sql-vm-ag-listener**设置。 **101-sql-vm-ag-setup**
   
   
要配置内部负载均衡器并创建可用性组侦听器，请执行以下操作：
1. 转到[101 sql-vm-aglistener 设置](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup)快速入门模板，然后选择 **"部署到 Azure"** 以在 Azure 门户中启动快速入门模板。
1. 填写配置内部负载均衡器所需的字段，并创建可用性组侦听器。 您可以将可选字段留空。 

   下表显示了模板的必要值： 

   | **字段** | “值” |
   | --- | --- |
   |**资源组** | SQL Server VM 和可用性组所在的资源组。 | 
   |**现有故障转移群集名称** | SQL Server VM 要加入到的群集的名称。 |
   | **现有 SQL 可用性组**| SQL Server VM 所属的可用性组的名称。 |
   | **现有 VM 列表** | 属于上述可用性组的 SQL Server VM 的名称。 用逗号和空格分隔名称（例如 *：SQLVM1、SQLVM2*）。 |
   | **侦听器** | 要分配给侦听器的 DNS 名称。 默认情况下，此模板指定名称"aglistener"，但您可以更改它。 名称不应超过 15 个字符。 |
   | **侦听程序端口** | 您希望侦听器使用的端口。 通常，此端口应默认为 1433。 这是模板指定的端口号。 但是，如果默认端口已更改，侦听器端口应改用该值。 | 
   | **侦听器 IP** | 您希望侦听器使用的 IP 地址。 此地址将在模板部署期间创建，因此提供尚未使用的地址。  |
   | **现有子网** | SQL Server VM 的内部子网的名称（例如：*默认值*）。 您可以通过访问**资源组**、选择虚拟网络、在 **"设置"** 窗格中选择**子网**以及复制**名称**下的值来确定此值。 |
   | **现有内部负载均衡器** | 您在步骤 3 中创建的内部负载均衡器的名称。 |
   | **探头端口** | 您希望内部负载均衡器使用的探头端口。 默认情况下，模板使用 59999，但您可以更改此值。 |
   | &nbsp; | &nbsp; |

1. 如果您同意条款和条件，请选择 **"我同意上述条款和条件"** 复选框。 选择 **"购买"** 以完成快速入门模板的部署。 
1. 要监视部署，请从顶部导航横幅中的 **"通知**"铃图标中选择部署，或者转到 Azure 门户中的**资源组**。 选择 **"设置"** 下的 **"部署**"，然后选择**Microsoft.模板**部署。 

>[!NOTE]
>如果部署中途失败，则需要在重新部署**101 sql-vm-aglistener 设置**快速入门模板之前，使用 PowerShell 手动[删除新创建的侦听器](#remove-the-availability-group-listener)。 

## <a name="remove-the-availability-group-listener"></a>删除可用性组侦听器
如果以后需要删除模板配置的可用性组侦听器，则必须通过 SQL VM 资源提供程序。 由于侦听器是通过 SQL VM 资源提供程序注册的，因此只需通过 SQL 服务器管理工作室删除侦听器是不够的。 

最好的方法是通过使用 PowerShell 中的以下代码段通过 SQL VM 资源提供程序将其删除。 这样做会从 SQL VM 资源提供程序中删除可用性组侦听器元数据。 它还从可用性组物理上删除侦听器。 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>常见错误
本部分讨论一些已知问题及其可能的解决方法。 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>可用性组“\<AG 名称>”的可用性组侦听程序已存在
在 Azure 快速入门模板中使用的可用性组侦听器中所使用的选定可用性组已包含侦听器。 它实际上在可用性组中，或者其元数据保留在 SQL VM 资源提供程序中。 在重新部署**101 sql-vm-aglistener 设置**快速启动模板之前，请使用[PowerShell](#remove-the-availability-group-listener)删除侦听器。 

### <a name="connection-only-works-from-primary-replica"></a>只能从主要副本进行连接
此行为可能来自失败的**101-sql-vm-aglistener 设置**模板部署，该部署使内部负载均衡器的配置处于不一致状态。 验证后端池是否列出可用性集，并且是否存在运行状况探测规则和负载均衡规则。 如果缺少任何内容，则内部负载均衡器的配置不一致。 

要解决此问题，请使用[PowerShell](#remove-the-availability-group-listener)删除侦听器，通过 Azure 门户删除内部负载均衡器，然后从步骤 3 重新开始。 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest - 只能更新 SQL 虚拟机列表
如果侦听器是通过 SQL 服务器管理工作室 （SSMS） 删除的，但未从 SQL VM 资源提供程序中删除，则部署**101-sql-vm-aglistener 设置**模板时可能会出现此错误。 通过 SSMS 删除侦听器不会从 SQL VM 资源提供程序中删除侦听器的元数据。 侦听器必须通过[PowerShell](#remove-the-availability-group-listener)从资源提供程序中删除。 

### <a name="domain-account-does-not-exist"></a>域帐户不存在
此错误可能有两个原因。 指定的域帐户不存在，或者缺少[用户主体名称 （UPN）](/windows/desktop/ad/naming-properties#userprincipalname)数据。 **101-sql-vm-ag 设置**模板期望 UPN 窗体中的域帐户（即*user@domain.com*），但某些域帐户可能缺少它。 当本地用户在服务器升级到域控制器时，或通过 PowerShell 创建用户时，通常会迁移为第一个域管理员帐户时，就会发生这种情况。 

验证该帐户是否存在。 如果是，则可能遇到第二种情况。 要解决此问题，可以执行以下操作：

1. 在域控制器上，通过“服务器管理器”中的“工具”选项打开“Active Directory 用户和计算机”窗口。************ 
2. 通过选择左侧窗格中的 **"用户"** 转到帐户。
3. 右键单击帐户，然后选择 **"属性**"。
4. 选择"**帐户**"选项卡。如果 **"用户登录名称**"框为空，则这是错误的原因。 

    ![用户帐户为空表明 UPN 缺失](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. 填写 **"用户登录名称**"框以匹配用户的名称，并从下拉列表中选择正确的域。 
6. 选择“应用”以保存所做的更改，然后选择“确定”，将对话框关闭。******** 

进行这些更改后，请尝试再次部署 Azure 快速入门模板。 



## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [SQL 服务器 VM 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL 服务器 VM 的常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL 服务器 VM 的定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL 服务器 VM 的发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [切换 SQL Server VM 的许可模型](virtual-machines-windows-sql-ahb.md)



