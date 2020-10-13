---
title: 配置可用性组（Azure 快速启动模板）
description: 使用 Azure 快速启动模板创建 Windows 故障转移群集、将 SQL Server VM 加入到群集、创建侦听程序，并在 Azure 中配置内部负载均衡器。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 207ee67c207f028b5f4bd45d99a7ef431429debb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91293560"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>使用 Azure 快速启动模板为 Azure VM 上的 SQL Server 配置可用性组
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍如何使用 Azure 快速启动模板来部分自动化在 Azure 中为 SQL Server 虚拟机 (VM) 部署 Always On 可用性组配置的过程。 此过程中使用了两个 Azure 快速启动模板： 

   | 模板 | 说明 |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | 创建 Windows 故障转移群集并将 SQL Server VM 加入其中。 |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | 创建可用性组侦听程序并配置内部负载均衡器。 使用此模板的前提是，Windows 故障转移群集是使用“101-sql-vm-ag-setup”模板创建的。 |
   | &nbsp; | &nbsp; |

其他可用性组配置部分（例如创建可用性组，以及创建内部负载均衡器）必须手动完成。 本文提供自动和手动步骤的顺序。
 

## <a name="prerequisites"></a>先决条件 
若要使用快速启动模板自动设置 Always On 可用性组，必须满足以下先决条件： 
- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 一个具有域控制器的资源组。 
- Azure 中的一个或多个已加入域的 VM，它们[运行 SQL Server 2016 Enterprise Edition（或更高版本）](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)，位于[已注册到 SQL VM 资源提供程序](sql-vm-resource-provider-register.md)的同一个可用性集或可用性区域中。  
- 两个可用的（未经任何实体使用的）IP 地址：一个用于内部负载均衡器，另一个用于与可用性组位于同一子网内的可用性组侦听程序。 如果已在使用负载均衡器，则只需要一个可用的 IP 地址。  

## <a name="permissions"></a>权限
若要使用 Azure 快速启动模板配置 Always On 可用性组，需要具备以下权限： 

- 在域中具有“创建计算机对象”权限的现有域用户帐户。  例如，域管理员帐户通常拥有足够的权限（如 account@domain.com）。 该帐户还应该属于每个 VM 上负责创建群集的本地管理员组。
- 可控制 SQL Server 的域用户帐户。 


## <a name="create-cluster"></a>创建群集
将 SQL Server VM 注册到 SQL VM 资源提供程序后，可将 SQL Server VM 加入到 SqlVirtualMachineGroups。 此资源定义 Windows 故障转移群集的元数据。 元数据包括版本、完全限定的域名、用于管理群集和 SQL 服务的 Active Directory 帐户，以及用作云见证的存储帐户。 

将 SQL Server VM 添加到 *SqlVirtualMachineGroups* 资源组会启动 Windows 故障转移群集服务，以便创建群集并将这些 SQL Server VM 加入该群集。 此步骤通过“101-sql-vm-ag-setup”快速启动模板自动执行。 可通过执行以下步骤实现它：

1. 转到 [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) 快速启动模板。 然后，选择“部署到 Azure”以在 Azure 门户中打开快速启动模板。
1. 填写必填字段，以配置 Windows 故障转移群集元数据。 可将可选字段留空。

   下表显示了模板的所需值： 

   | **字段** | 值 |
   | --- | --- |
   | **订阅** |  SQL Server VM 所在的订阅。 |
   |**资源组** | SQL Server VM 所在的资源组。 | 
   |**故障转移群集名称** | 要用于新 Windows 故障转移群集的名称。 |
   | **现有 VM 列表** | 要加入到可用性组并成为此新群集一部分的 SQL Server VM。 请用逗号和空格分隔这些值（例如：“SQLVM1, SQLVM2”）。 |
   | **SQL Server 版本** | SQL Server VM 的 SQL Server 版本。 从下拉列表中选择它。 当前仅支持 SQL Server 2016 和 SQL Server 2017 映像。 |
   | **现有的完全限定域名** | SQL Server VM 所在的域的现有 FQDN。 |
   | **现有域帐户** | 一个现有的域用户帐户，该帐户具有在域中“创建计算机对象”的权限，因为 [CNO](/windows-server/failover-clustering/prestage-cluster-adds) 是在模板部署过程中创建的。 例如，域管理员帐户通常拥有足够的权限（如 account@domain.com）。 该帐户还应该属于每个 VM 上负责创建群集的本地管理员组。| 
   | **域帐户密码** | 上述域用户帐户的密码。 | 
   | **现有 SQL 服务帐户** | 用于在可用性组部署过程中控制 [SQL Server 服务](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions)的域用户帐户（如 account@domain.com）。 |
   | **SQL 服务密码** | 用于控制 SQL Server 的域用户帐户所使用的密码。 |
   | **云见证名称** | 将要创建并用于云见证的一个新 Azure 存储帐户。 可以修改此名称。 |
   | **\_artifacts 位置** | 此字段默认设置，不应修改。 |
   | **\_artifacts 位置 SaS 令牌** | 此字段特意留空。 |
   | &nbsp; | &nbsp; |

1. 如果你同意条款和条件，请选择“我同意上述条款和条件”复选框。 然后选择“购买”以完成快速启动模板的部署。 
1. 若要监视部署，请从顶部导航横幅的“通知”钟形图标中选择“部署”，或转到 Azure 门户中的“资源组” 。 在“设置”下选择“部署”，然后选择“Microsoft.Template”部署  。 

>[!NOTE]
> 模板部署过程中提供的凭据仅在部署期间存储。 部署完成后，将删除这些密码。 如果要向群集中添加更多 SQL Server VM，需要再次提供这些密码。 



## <a name="validate-cluster"></a>验证群集 

要使故障转移群集受 Microsoft 支持，它必须通过群集验证。 使用首选方法（如远程桌面协议 () RDP）连接到 VM，并验证群集是否通过验证，然后再继续。 否则，群集将处于不受支持状态。 

你可以使用故障转移群集管理器 (FCM) 或以下 PowerShell 命令来验证群集：

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```


## <a name="create-availability-group"></a>创建可用性组 
像平时一样使用 [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)、[PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell) 或 [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql) 手动创建可用性组。 

>[!IMPORTANT]
> 此时请不要创建侦听程序，因为 101-sql-vm-aglistener-setup 快速启动模板会在步骤 4 中自动创建侦听程序。 

## <a name="create-load-balancer"></a>创建负载均衡器
Always On 可用性组侦听器需要 Azure 负载均衡器的内部实例。 内部负载均衡器为可用性组侦听器提供“浮动”IP 地址，可以加快故障转移和重新连接的速度。 如果可用性组中的 SQL Server VM 属于同一个可用性集，则可以使用基本负载均衡器。 否则，需要使用标准负载均衡器。 

> [!IMPORTANT]
> 内部负载均衡器应与 SQL Server VM 实例位于同一虚拟网络中。 

只需创建内部负载均衡器。 在步骤 4 中，101-sql-vm-aglistener-setup 快速启动模板将处理其余的配置（例如后端池、运行状况探测和负载均衡规则）。 

1. 在 Azure 门户中，打开包含 SQL Server 虚拟机的资源组。 
2. 在资源组中，选择“添加”。
3. 搜索“负载均衡器”。 在搜索结果中，选择“负载均衡器”（由 Microsoft 发布） 。
4. 在“负载均衡器”边栏选项卡上，选择“创建” 。
5. 在“创建负载均衡器”对话框中配置负载均衡器，如下所示：

   | 设置 | 值 |
   | --- | --- |
   | **名称** |输入用于表示负载均衡器的文本名称。 例如，输入“sqlLB”。 |
   | 类型 |**内部**：大多数实施方案使用内部负载均衡器，它可让同一虚拟网络中的应用程序连接到可用性组。  </br> **外部**：可让应用程序通过公共 Internet 连接连接到可用性组。 |
   | **虚拟网络** | 选择 SQL Server 实例所在的虚拟网络。 |
   | **子网** | 选择 SQL Server 实例所在的子网。 |
   | IP 地址分配 |**静态** |
   | **专用 IP 地址** | 指定子网中的某个可用 IP 地址。 |
   | **订阅** |如果有多个订阅，可能会显示此字段。 选择要与此资源关联的订阅。 它通常是与可用性组的所有资源相同的订阅。 |
   | **资源组** |选择 SQL Server 实例所在的资源组。 |
   | **位置** |选择 SQL Server 实例所在的 Azure 位置。 |
   | &nbsp; | &nbsp; |

6. 选择“创建”。 


>[!IMPORTANT]
> 每个 SQL Server VM 的公共 IP 资源应有一个与标准负载均衡器兼容的标准 SKU。 若要确定 VM 公共 IP 资源的 SKU，请转到“资源组”，选择 SQL Server VM 的“公共 IP 地址”资源，并在“概述”窗格的“SKU”下面找到该值   。 

## <a name="create-listener"></a>创建侦听器 

使用 101-sql-vm-aglistener-setup 快速启动模板自动创建可用性组侦听程序并配置内部负载均衡器。 该模板预配 Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener 资源。 **101-sql-vm-aglistener-setup** 快速入门模板通过 SQL VM 资源提供程序执行以下操作：

- 为侦听器创建新的前端 IP 资源（基于在部署过程中提供的 IP 地址值）。 
- 为群集和内部负载均衡器配置网络设置。 
- 为内部负载均衡器、运行状况探测和负载均衡规则配置后端池。
- 使用给定的 IP 地址和名称创建可用性组侦听程序。
 
>[!NOTE]
> 可以使用 101-sql-vm-aglistener-setup，前提是 Windows 故障转移群集是使用 101-sql-vm-ag-setup 模板创建的 。
   
   
若要配置内部负载均衡器并创建可用性组侦听程序，请执行以下操作：
1. 转到 [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) 快速启动模板，然后选择“部署到 Azure”以在 Azure 门户中启动快速启动模板。
1. 填写必填字段以配置内部负载均衡器，并创建可用性组侦听程序。 可将可选字段留空。 

   下表显示了模板的所需值： 

   | **字段** | 值 |
   | --- | --- |
   |**资源组** | SQL Server VM 和可用性组所在的资源组。 | 
   |**现有故障转移群集名称** | SQL Server VM 要加入到的群集的名称。 |
   | **现有 SQL 可用性组**| SQL Server VM 所属的可用性组的名称。 |
   | **现有 VM 列表** | 属于上述可用性组的 SQL Server VM 的名称。 请用逗号和空格分隔名称（例如：“SQLVM1, SQLVM2”）。 |
   | **侦听器** | 要分配给侦听程序的 DNS 名称。 默认情况下，此模板指定名称“aglistener”，但可以对其进行更改。 名称不应超过 15 个字符。 |
   | **侦听程序端口** | 希望侦听程序使用的端口。 通常情况下，此端口应为默认端口 1433。 这是模板指定的端口号。 但是，如果更改了默认端口，则侦听程序端口应改用该值。 | 
   | **侦听器 IP** | 希望侦听程序使用的 IP 地址。 此地址将在模板部署过程中创建，因此请提供一个未在使用的 IP 地址。  |
   | **现有子网** | SQL Server VM 内部子网的名称（例如：default）。 可以通过以下方法确定此值：转到“资源组”，选择你的虚拟网络，在“设置”窗格中选择“子网”，然后复制“名称”下面的值   。 |
   | **现有内部负载均衡器** | 在步骤 3 中创建的内部负载均衡器的名称。 |
   | **探测端口** | 希望内部负载均衡器使用的探测端口。 该模板默认使用 59999，但可以更改此值。 |
   | &nbsp; | &nbsp; |

1. 如果你同意条款和条件，请选择“我同意上述条款和条件”复选框。 选择“购买”以完成快速启动模板的部署。 
1. 若要监视部署，请从顶部导航横幅的“通知”钟形图标中选择“部署”，或转到 Azure 门户中的“资源组” 。 在“设置”下选择“部署”，然后选择“Microsoft.Template”部署  。 

>[!NOTE]
>如果部署中途失败，则需要使用 PowerShell 手动[删除新建的侦听程序](#remove-listener)，然后重新部署 101-sql-vm-aglistener-setup 快速启动模板。 

## <a name="remove-listener"></a>删除侦听器
如果以后需要删除该模板配置的可用性组侦听程序，则必须通过 SQL VM 资源提供程序执行整个操作。 由于侦听程序是通过 SQL VM 资源提供程序注册的，因此仅仅通过 SQL Server Management Studio 删除它是不够的。 

最佳方法是在 PowerShell 中使用以下代码片段，通过 SQL VM 资源提供程序将其删除。 这样会从 SQL VM 资源提供程序中删除可用性组侦听程序元数据。 并将侦听程序从可用性组中实际删除。 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>常见错误
本部分讨论一些已知问题及其可能的解决方法。 

可用性**组 "" 的可用性组侦听器 \<AG-Name> 已存在**：用于可用性组侦听器的 Azure 快速入门模板中所选的可用性组已包含一个侦听器。 这表明侦听程序在物理上位于可用性组内，或者其元数据仍保留在 SQL VM 资源提供程序内。 使用 [PowerShell](#remove-listener) 删除该侦听程序，然后重新部署 101-sql-vm-aglistener-setup 快速启动模板。 

**连接仅适用于主副本** 此行为可能来自已导致内部负载均衡器配置处于不一致状态的失败的 **101-aglistener** 模板部署。 验证后端池是否列出可用性集，并且是否存在运行状况探测规则和负载均衡规则。 如果缺少任何内容，则内部负载均衡器的配置将处于不一致状态。 

若要解决此行为，请使用 [PowerShell](#remove-listener) 删除侦听程序，通过 Azure 门户删除内部负载均衡器，然后从步骤 3 重新开始。 

**BadRequest-只能更新 SQL 虚拟机列表** 如果侦听器已通过 SQL Server Management Studio (SSMS) 删除，但未从 SQL VM 资源提供程序中删除，则在 **101 部署 aglistener** 模板时，可能会发生此错误。 通过 SSMS 删除侦听程序不会将侦听程序的元数据从 SQL VM 资源提供程序中删除。 必须通过 [PowerShell](#remove-listener) 从资源提供程序中删除该侦听程序。 

**域帐户不存在** 此错误可能有两个原因。 指定的域帐户不存在，或者缺少[用户主体名称 (UPN)](/windows/desktop/ad/naming-properties#userprincipalname) 数据。 101-sql-vm-ag-setup 模板要求域帐户采用 UPN 形式（即 user@domain.com），但某些域帐户可能缺少它。 如果本地用户在迁移后成为第一个域管理员帐户（此时服务器已提升为域控制器），或者如果用户是通过 PowerShell 创建的，则通常会发生这种情况。 

验证帐户是否存在。 如果帐户存在，则你遇到的是第二种情况。 若要解决此问题，请执行以下操作：

1. 在域控制器上，通过“服务器管理器”中的“工具”选项打开“Active Directory 用户和计算机”窗口。   
2. 在左侧窗格中选择“用户”以转到帐户。
3. 右键单击该帐户，然后选择“属性”。
4. 选择“帐户”选项卡。如果“用户登录名”框为空，则这是导致出现错误的原因。 

    ![用户帐户为空表明 UPN 缺失](./media/availability-group-quickstart-template-configure/account-missing-upn.png)

5. 填充与用户名称匹配的“用户登录名”框，然后从下拉列表中选择适当的域。 
6. 选择“应用”以保存所做的更改，然后选择“确定”，将对话框关闭。  

完成这些更改后，请尝试再次部署 Azure 快速启动模板。 


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [SQL Server VM 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server VM 的常见问题解答](frequently-asked-questions-faq.md)
* [SQL Server VM 的定价指南](pricing-guidance.md)
* [SQL Server VM 的发行说明](../../database/doc-changes-updates-release-notes.md)
* [切换 SQL Server VM 的许可模型](licensing-model-azure-hybrid-benefit-ahb-change.md)



