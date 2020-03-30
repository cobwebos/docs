---
title: Azure 的 Windows 虚拟机上的 SQL Server 常见问题解答 | Microsoft Docs
description: 本文提供有关运行 Azure VM 中的 SQL Server 时遇到的常见问题的解答。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 3b73c329c3db54ba78db15ced8e919af4d4a45d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249732"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Azure 的 Windows 虚拟机上运行的 SQL Server 常见问题解答

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

本文提供有关[在 Azure 的 Windows 虚拟机上运行 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 时出现的一些最常见问题的解答。

> [!NOTE]
> 本文重点阐述在 Windows VM 上运行 SQL Server 的特定问题。 如果在 Linux VM 上运行 SQL Server，请参阅 [Linux 常见问题](../../linux/sql/sql-server-linux-faq.md)。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>图像

1. **有哪些 SQL Server 虚拟机库映像可用？** 

   Azure 为所有 Windows 和 Linux 版本中的所有受支持 SQL Server 主要发行版维护虚拟机映像。 有关详细信息，请参阅 [Windows VM 映像](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)和 [Linux VM 映像](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create)的完整列表。

1. **现有的 SQL Server 虚拟机库映像是否会更新？**

   每隔两个月，都会使用最新的 Windows 和 Linux 更新对虚拟机库中的 SQL Server 映像进行更新。 对于 Windows 映像，这包括 Windows 更新中标记为重要的任何更新，以及重要的 SQL Server 安全更新和 Service Pack。 对于 Linux 映像，这包括最新的系统更新。 Linux 和 Windows 的 SQL Server 累积更新以不同的方式进行处理。 对于 Linux，SQL Server 累积更新也包含在刷新中。 但目前，Windows VM 不会连同 SQL Server 或 Windows Server 累积更新一起更新。

1. **是否可以从库中删除 SQL Server 虚拟机映像？**

   是的。 Azure 只为每个主要版本维护一个映像。 例如，发布新的 SQL Server Service Pack 时，Azure 会将新映像添加到该 Service Pack 的库。 先前 Service Pack 的 SQL Server 映像将立即从 Azure 门户中删除。 但是，在接下来的三个月，仍可以通过 PowerShell 预配该映像。 三个月之后，先前的 Service Pack 映像不再可用。 如果 SQL Server 版本由于生命周期结束而不受支持，则也会应用此删除策略。


1. **是否可以部署 Azure 门户中不可见的 SQL Server 的旧映像？**

   是的，使用 PowerShell。 有关使用 PowerShell 部署 SQL Server VM 的详细信息，请参阅[如何使用 Azure PowerShell 预配 SQL Server 虚拟机](virtual-machines-windows-ps-sql-create.md)。

1. **是否可以创建 SQL Server VM 的通用 Azure SQL Server 应用商店映像，并用它来部署 VM？**

   是，但随后您必须[将每个 SQL Server VM 注册到 SQL Server VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)，以便在门户中管理 SQL Server VM，以及利用自动修补和自动备份等功能。 在资源提供程序注册时，还需要为每个 SQL Server VM 指定许可证类型。 

1. **是否可以使用我自己的 VHD 来部署 SQL Server VM？**

   是，但随后您必须[将每个 SQL Server VM 注册到 SQL Server VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)，以便在门户中管理 SQL Server VM，以及利用自动修补和自动备份等功能。

1. **是否可以设置虚拟机库中未显示的配置（例如 Windows 2008 R2 + SQL Server 2012）？**

   不是。 对于包含 SQL Server 的虚拟机图库映像，必须通过 Azure 门户或 [PowerShell](virtual-machines-windows-ps-sql-create.md) 选择提供的某个映像。 但是，可以部署一个 Windows VM 并在其中自行安装 SQL Server。 然后，您必须[向 SQL Server VM 资源提供程序注册 SQL Server VM，](virtual-machines-windows-sql-register-with-resource-provider.md)以便在门户中管理 SQL Server VM，以及利用自动修补和自动备份等功能。 


## <a name="creation"></a>创建

1. **如何创建装有 SQL Server 的 Azure 虚拟机？**

   最简单的方法是创建包含 SQL Server 的虚拟机。 有关注册 Azure 并从门户创建 SQL Server VM 的教程，请参阅[在 Azure 门户中预配 SQL Server 虚拟机](virtual-machines-windows-portal-sql-server-provision.md)。 可选择使用按秒付费 SQL Server 许可的虚拟机映像，或者可以使用允许自带 SQL Server 许可证的映像。 此外，你也可以选用免费许可版（开发人员版或速成版），或通过重新使用本地许可证在 VM 上手动安装 SQL Server。 请务必向[SQL Server VM 资源提供程序注册 SQL Server VM，](virtual-machines-windows-sql-register-with-resource-provider.md)以便在门户中管理 SQL Server VM，以及利用自动修补和自动备份等功能。 如果自带许可，必须[在 Azure 上通过软件保障实现许可证移动性](https://azure.microsoft.com/pricing/license-mobility/)。 有关详细信息，请参阅 [SQL Server Azure VM 定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)。

1. **如何将本地 SQL Server 数据库迁移到云中？**

   首先，请创建装有 SQL Server 实例的 Azure 虚拟机。 然后将本地数据库迁转到该实例。 有关数据迁移策略，请参阅[将 SQL Server 数据库迁移到 Azure VM 中的 SQL Server](virtual-machines-windows-migrate-sql.md)。

## <a name="licensing"></a>授权

1. **如何在 Azure VM 上安装 SQL Server 的许可版本？**

   有三种方法可以做到这一点。 企业协议 (EA) 客户可以预配[支持许可证的虚拟机映像](virtual-machines-windows-sql-server-iaas-overview.md#BYOL)之一，也称为自带许可 (BYOL)。 如果您有[软件保证](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)，则可以在现有即用即付 （PAYG） 映像上启用[Azure 混合权益](virtual-machines-windows-sql-ahb.md)。 或者，可将 SQL Server 安装媒体复制到 Windows Server VM，然后在 VM 上安装 SQL Server。 请务必向[资源提供商](virtual-machines-windows-sql-register-with-resource-provider.md)注册 SQL Server VM，以访问门户管理、自动备份和自动修补等功能。 

1. **如果已通过即用即付库映像之一创建了 VM，是否可以将该 VM 更改为使用自己的 SQL Server 许可证？**

   是的。 通过启用[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)，可以轻松地切换即用即付 （PAYG） 库映像，以自带许可证 （BYOL）。  有关详细信息，请参阅[如何更改 SQL Server VM 的许可模型](virtual-machines-windows-sql-ahb.md)。 目前，此功能仅面向公有云客户提供。

1. **切换许可模型是否需要将 SQL Server 停机？**

   不是。 [更改许可模型](virtual-machines-windows-sql-ahb.md)不需将 SQL Server 停机，因为更改会立即生效，不需重启 VM。 但是，要将 SQL Server VM 注册到 SQL Server VM 资源提供程序[，SQL IaaS 扩展](virtual-machines-windows-sql-server-agent-extension.md)是先决条件，_在全_模式下安装 SQL IaaS 扩展将重新启动 SQL Server 服务。 因此，如果需要安装 SQL IaaS 扩展，请将其安装在_轻型_模式下，以获得有限的功能，或者在维护时段内以_全_模式安装。 在_轻量级_模式下安装的 SQL IaaS 扩展可以随时升级到_完全_模式，但需要重新启动 SQL Server 服务。 
   
1. **是否可以在使用经典模型部署的 SQL Server VM 上切换许可模型？**

   不是。 不支持在经典 VM 上更改许可模型。 可将 VM 迁移到 Azure 资源管理器模型，并将其注册到 SQL Server VM 资源提供程序。 将 VM 注册到 SQL Server VM 资源提供程序后，即可在 VM 上更改许可模型。

1. **是否可以使用 Azure 门户管理同一 VM 上的多个实例？**

   不是。 门户管理是 SQL Server VM 资源提供程序提供的一项功能，它依赖于 SQL Server IaaS 代理扩展。 因此，与扩展一样，资源提供程序也适用相同的限制。 门户只能管理一个默认实例，也可以管理一个命名实例，只要它配置正确。 有关这些限制的详细信息，请参阅 SQL [Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)。 

1. **CSP 订阅是否能够激活 Azure 混合权益？**

   能，Azure 混合权益适用于 CSP 订阅。 CSP 客户应首先部署即用即付映像，然后[将许可模式更改](virtual-machines-windows-sql-ahb.md)为自带许可。
   
 
1. **如果 SQL Server 仅用于待机/故障转移，是否必须付费才能在 Azure VM 上为 SQL Server 授予许可？**

   要为备用辅助可用性组或故障转移群集实例提供免费被动许可证，必须满足[产品许可条款](https://www.microsoft.com/licensing/product-licensing/products)概述的所有以下条件：

   1. 您可以通过[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)[获得许可证移动性](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2)。 
   1. 被动 SQL Server 实例不会为客户端提供 SQL Server 数据，也不会运行活动的 SQL Server 工作负荷。 它只用于与主服务器同步，或者使被动数据库保持热备用状态。 如果它提供数据（例如向运行活动 SQL Server 工作负荷的客户端报告）或执行产品术语中指定的任何工作以外的任何工作，则它必须是付费许可的 SQL Server 实例。 辅助实例允许以下活动：数据库一致性检查或 CheckDB、完整备份、事务日志备份和监视资源使用情况数据。 您还可以在 90 天内同时运行主灾难恢复实例，进行短暂的灾难恢复测试。 
   1. 活动的 SQL Server 许可证已涵盖在软件保障中，仅允许**一个**被动辅助 SQL Server 实例，允许的计算量不能超过已许可的活动服务器。 
   1. 辅助 SQL Server VM 利用 Azure 门户中的[灾难恢复](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)许可证。
   
1. **什么是被动实例？**

   被动 SQL Server 实例不会为客户端提供 SQL Server 数据，也不会运行活动的 SQL Server 工作负荷。 它只用于与主服务器同步，或者使被动数据库保持热备用状态。 如果它提供数据（例如向运行活动 SQL Server 工作负荷的客户端报告）或执行产品术语中指定的任何工作以外的任何工作，则它必须是付费许可的 SQL Server 实例。 辅助实例允许以下活动：数据库一致性检查或 CheckDB、完整备份、事务日志备份和监视资源使用情况数据。 您还可以在 90 天内同时运行主灾难恢复实例，进行短暂的灾难恢复测试。
   

1. **哪些方案可以利用"厌恶者恢复 （DR）"好处？**

   [许可指南](https://aka.ms/sql2019licenseguide)提供了可以使用灾难恢复收益的方案。 有关详细信息，请参阅您的产品条款并与您的许可联系人或客户经理联系。

1. **哪些订阅支持灾难恢复 （DR） 权益？**

   提供软件保障等价订阅权限作为固定权益的综合计划支持 DR 权益。 这包括。 但不限于开放值 （OV）、开放价值订阅 （OVS）、企业协议 （EA）、企业订阅协议 （EAS） 以及服务器和云注册 （SCE）。 有关详细信息，请参阅[产品条款](https://www.microsoft.com/licensing/product-licensing/products)并与您的许可联系人或 acocunt 经理联系。 

   
 ## <a name="resource-provider"></a>资源提供程序

1. **向新的 SQL Server VM 资源提供程序注册 VM 是否会带来额外费用？**

   不是。 SQL Server VM 资源提供程序只是支持 Azure VM 上的 SQL Server 的其他可管理性，无需支付额外费用。 

1. **SQL Server VM 资源提供程序是否适用于所有客户？**
 
   是的，只要 SQL Server VM 使用资源管理器模型部署在公共云上，而不是使用经典模型。 所有其他客户都能够向新的 SQL Server VM 资源提供程序注册。 但是，只有具有[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)权益的客户才能通过在 SQL Server VM 上激活[Azure 混合权益 （AHB）](https://azure.microsoft.com/pricing/hybrid-benefit/)来使用自己的许可证。 

1. **如果移动或删除 VM 资源，资源提供程序 （_Microsoft.SqlVirtualMachine_） 资源会发生什么情况？** 

   删除或移动 Microsoft.Compute/VirtualMachine 资源时，会通知关联的 Microsoft.SqlVirtualMachine 资源以异步方式复制此操作。

1. **如果资源提供程序 （_Microsoft.SqlVirtual计算机_） 资源被丢弃，VM 会发生什么情况？**

    删除 Microsoft.SqlVirtualMachine 资源时，Microsoft.Compute/VirtualMachine 资源不受影响。 但是，许可更改会默认回退到原始的映像源。 

1. **是否可以向 SQL Server VM 资源提供程序注册自部署的 SQL Server VM VM？**

    是的。 如果从自己的媒体部署 SQL Server，并安装 SQL IaaS 扩展，则可将 SQL Server VM 注册到资源提供程序，以便获取 SQL IaaS 扩展提供的可管理性权益。 但是，您无法将自部署的 SQL Server VM 转换为即用即付。


   


## <a name="administration"></a>管理

1. **是否可以在同一 VM 上安装 SQL Server 的第二个实例？是否可以更改默认实例的已安装功能？**

   是的。 SQL Server 安装介质位于**C**驱动器上的文件夹中。 从该位置运行**安装程序.exe**以添加新 SQL Server 实例或更改计算机上的 SQL Server 的其他已安装功能。 请注意，某些功能（例如自动备份、自动修补和 Azure Key Vault 集成）仅对默认实例或者正确配置的命名实例起作用（请参阅“问题 3”）。 

1. **是否可以卸载 SQL Server 的默认实例？**

   可以，但需注意以下事项。 首先，根据 VM 的许可模型，与 SQL Server 相关的计费可能会继续发生。 其次，如前面的解答中所述，某些功能依赖于 [SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)。 如果卸载默认实例但未同时删除 IaaS 扩展，该扩展会继续查找默认实例并可能生成事件日志错误。 这些错误来自以下两个来源：**Microsoft SQL Server 凭据管理**和 **Microsoft SQL Server IaaS 代理**。 其中一个错误可能类似于以下内容：

      建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 找不到或无法访问服务器。

   如果确实决定卸载默认实例，也可以卸载[SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)。 

1. **是否可将 SQL Server 的命名实例与 IaaS 扩展配合使用**？
   
   如果该命名实例是 SQL Server 上的唯一实例，并且[正确卸载](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance)了原始的默认实例，则可以这样做。 如果没有默认实例，但是单个 SQL Server VM 上有多个命名实例，则 SQL Server IaaS 代理扩展将无法安装。 

1. **是否可从 SQL Server VM 中完全删除 SQL Server？**

   是的，但仍将按照 [SQL Server Azure VM 的定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)收取 SQL Server VM 费用。 如果不再需要 SQL Server，可以部署新的虚拟机并将数据和应用程序迁移到新的虚拟机。 然后可以删除 SQL Server 虚拟机。
   
## <a name="updating-and-patching"></a>更新和修补

1. **如何将 Azure VM 中的 SQL Server 更改为另一版本？**

   客户可以使用包含所需 SQL Server 版本的安装介质来更改其 SQL Server 版本。 更改版本以后，使用 Azure 门户修改 VM 的版本属性，使之准确反映 VM 的计费。 有关详细信息，请参阅[SQL Server VM 的更改版本](virtual-machines-windows-sql-change-edition.md)。 不同版本的 SQL Server 没有计费差异，因此一旦 SQL Server 版本更改，就无需执行进一步操作。

1. **我在哪里可以获取设置介质来更改 SQL Server 的版本或版本？**

   拥有[软件保证](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)的客户可以从[批量许可中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)获得其安装介质。 没有软件保证的客户可以使用具有所需版本的应用商店 SQL Server VM 映像中的设置媒体。
   
1. **如何将更新和服务包应用到 SQL Server VM？**

   虚拟机允许控制主机，包括应用更新的时间与方法。 对于操作系统，可以手动应用 Windows 更新，或者启用名为[自动修补](virtual-machines-windows-sql-automated-patching.md)的计划服务。 自动修补将安装任何标记为重要的更新，包括该类别中的 SQL Server 更新。 必须手动安装其他可选的 SQL Server 更新。

1. **我可以在 SQL Server VM 资源提供程序注册 SQL Server 2008 / 2008 R2 实例后升级它吗？**

   是的。 您可以使用任何设置媒体来升级 SQL Server 的版本和版本，然后可以将[SQL IaaS 扩展模式](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)从_无代理_升级到_完整_。 这样做将使您能够访问 SQL IaaS 扩展的所有优势，如门户可管理性、自动备份和自动修补。 

1. **如何为支持 SQL Server 2008 和 SQL Server 2008 R2 实例的末尾获得免费扩展的安全更新？**

   通过将 SQL Server 按"按"情况移动到 Azure SQL 虚拟机，可以获取[免费扩展的安全更新](virtual-machines-windows-sql-server-2008-eos-extend-support.md)。 有关详细信息，请参阅[支持选项的结尾](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)。 
  
   

## <a name="general"></a>常规

1. **Azure VM 上支持 SQL Server 故障转移群集实例 （FCI）吗？**

   是的。 您可以使用[高级文件共享 （PFS）](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)或存储子系统[的直接存储空间 （S2D）](virtual-machines-windows-portal-sql-create-failover-cluster.md)安装故障转移群集实例。 高级文件共享提供 IOPS 和吞吐量容量，可满足许多工作负载的需求。 对于 IO 密集型工作负载，请考虑直接使用基于人造高级磁盘或超磁盘的存储空间。 或者，可使用第三方群集或存储解决方案，如 [Azure 虚拟机中 SQL Server 的高可用性和灾难恢复](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)中所述。

   > [!IMPORTANT]
   > 目前，Azure 上的 SQL Server FCI 不支持_完整的_ [SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)。 我们建议你从参与 FCI 的 VM 中卸载_完整_扩展，并改为在_轻型_模式下安装该扩展。 此扩展支持自动备份和修补之类的功能，以及适用于 SQL Server 的某些门户功能。 卸载_完整_代理以后，这些功能将不适用于 SQL Server VM。

1. **SQL Server VM 与 SQL 数据库服务之间的差别是什么？**

   从概念上讲，在 Azure 虚拟机上运行 SQL Server 与在远程数据中心运行 SQL Server 并没什么不同。 相比之下， [SQL 数据库](../../../sql-database/sql-database-technical-overview.md) 可提供数据库即服务。 使用 SQL 数据库时，无法访问托管数据库的计算机。 有关完整比较，请参阅[选择云 SQL Server 选项：Azure SQL (PaaS) 数据库或 Azure VM 上的 SQL Server (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)。

1. **如何在 Azure VM 上安装 SQL 数据工具？**

    从 [Microsoft SQL Server 数据工具 - Visual Studio 2013 商业智能](https://www.microsoft.com/download/details.aspx?id=42313)下载并安装 SQL 数据工具。

1. **SQL Server VM 是否支持使用 MSDTC 的分布式事务？**
   
    是的。 SQL Server 2016 SP2 和更高版本支持本地 DTC。 但是，在使用 Always On 可用性组时必须对应用程序进行测试，因为故障转移期间正在进行的事务将会失败，并且必须重试。 从 Windows Server 2019 开始将会推出群集 DTC。 

## <a name="resources"></a>资源

**Windows VM**：

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)。
* [设置 SQL Server Windows VM](virtual-machines-windows-portal-sql-server-provision.md)
* [将数据库迁移到 Azure VM 上的 SQL 服务器](virtual-machines-windows-migrate-sql.md)
* [Azure 虚拟机中 SQL Server 的高可用性和灾难恢复](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure 虚拟机中 SQL Server 的性能最佳实践](virtual-machines-windows-sql-performance.md)
* [Azure 虚拟机中的 SQL Server 的应用程序模式和开发策略](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**：

* [Linux VM 上的 SQL Server 概述](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [设置 SQL Server Linux VM](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [常见问题 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [“Linux 上的 SQL Server”文档](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
