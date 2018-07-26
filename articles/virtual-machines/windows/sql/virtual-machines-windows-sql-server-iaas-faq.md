---
title: Windows Azure 虚拟机上的 SQL Server 常见问题 | Microsoft 文档
description: 本文提供有关运行 Azure VM 中的 SQL Server 时遇到的常见问题的解答。
services: virtual-machines-windows
documentationcenter: ''
author: v-shysun
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: v-shysun
ms.openlocfilehash: 48df858095cb867954460ec858567e41ed330063
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39012065"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-azure-virtual-machines"></a>Microsoft Azure 虚拟机上运行的 SQL Server 的常见问题

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

本文提供有关[在 Windows Azure 虚拟机上运行 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 时出现的一些最常见问题的解答。

> [!NOTE]
> 本文重点阐述在 Windows VM 上运行 SQL Server 的特定问题。 如果在 Linux VM 上运行 SQL Server，请参阅 [Linux 常见问题](../../linux/sql/sql-server-linux-faq.md)。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a>映像

1. **有哪些 SQL Server 虚拟机库映像可用？**

   Azure 为所有 Windows 和 Linux 版本中的所有受支持 SQL Server 主要发行版维护虚拟机映像。 有关更多详细信息，请参阅 [Windows VM 映像](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)和 [Linux VM 映像](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create)的完整列表。

1. **现有的 SQL Server 虚拟机库映像是否会更新？**

   每隔两个月，都会使用最新的 Windows 和 Linux 更新对虚拟机库中的 SQL Server 映像进行更新。 对于 Windows 映像，这包括 Windows 更新中标记为重要的任何更新，以及重要的 SQL Server 安全更新和 Service Pack。 对于 Linux 映像，这包括最新的系统更新。 Linux 和 Windows 的 SQL Server 累积更新以不同的方式进行处理。 对于 Linux，SQL Server 累积更新也包含在刷新中。 但目前，Windows VM 不会连同 SQL Server 或 Windows Server 累积更新一起更新。

1. **是否可以从库中删除 SQL Server 虚拟机映像？**

   是的。 Azure 只为每个主要版本维护一个映像。 例如，发布新的 SQL Server Service Pack 时，Azure 会将新映像添加到该 Service Pack 的库。 先前 Service Pack 的 SQL Server 映像将立即从 Azure 门户中删除。 但是，在接下来的三个月，仍可以通过 PowerShell 预配该映像。 三个月之后，先前的 Service Pack 映像不再可用。 如果 SQL Server 版本由于生命周期结束而不受支持，则也会应用此删除策略。

1. **是否可以从 SQL Server VM 创建 VHD 映像？**

   可以，但需注意以下事项。 如果将此 VHD 部署到 Azure 中的新 VM，则无法在门户网站中获取 SQL Server 配置部分。 此时必须通过 PowerShell 管理 SQL Server 配置选项。 此外，将会按该映像最初基于的 SQL VM 的费率进行计费。 即使在部署前已从 VHD 中删除 SQL Server，也是如此。 

1. **是否可以设置虚拟机库中未显示的配置（例如 Windows 2008 R2 + SQL Server 2012）？**

   不是。 对于包含 SQL Server 的虚拟机库映像，必须选择提供的的映像之一。

## <a name="creation"></a>创建

1. **如何创建装有 SQL Server 的 Azure 虚拟机？**

   最简单的解决方法是创建包含 SQL Server 的虚拟机。 有关注册 Azure 并从门户创建 SQL VM 的教程，请参阅[在 Azure 门户中预配 SQL Server 虚拟机](virtual-machines-windows-portal-sql-server-provision.md)。 可选择使用按秒付费 SQL Server 许可的虚拟机映像，或者可以使用允许自带 SQL Server 许可证的映像。 此外，你也可以选用免费许可版（开发人员版或速成版），或通过重新使用本地许可证在 VM 上手动安装 SQL Server。 如果自带许可，必须[在 Azure 上通过软件保障实现许可证移动性](https://azure.microsoft.com/pricing/license-mobility/)。 有关详细信息，请参阅 [SQL Server Azure VM 定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)。

1. **如何将本地 SQL Server 数据库迁移到云中？**

   首先，请创建装有 SQL Server 实例的 Azure 虚拟机。 然后将本地数据库迁转到该实例。 有关数据迁移策略，请参阅[将 SQL Server 数据库迁移到 Azure VM 中的 SQL Server](virtual-machines-windows-migrate-sql.md)。

## <a name="licensing"></a>许可

1. **如何在 Azure VM 上安装 SQL Server 的许可版本？**

   可通过两种方式来执行此操作。 可以预配[支持许可证的虚拟机映像](virtual-machines-windows-sql-server-iaas-overview.md#BYOL)之一，也称为自带许可 (BYOL)。 另一个选项是将 SQL Server 安装介质复制到 Windows Server VM 上，然后在 VM 上安装 SQL Server。 但是，如果手动安装 SQL Server，则没有门户集成，并且不支持 SQL Server IaaS 代理扩展，因此“自动备份”和“自动修补”等功能在此方案中不起作用。 出于此原因，我们建议使用 BYOL 库映像之一。 要在 Azure VM 上使用 BYOL 或自己的 SQL Server 媒体，必须获得 [Azure 上通过软件保障实现的许可移动性](https://azure.microsoft.com/pricing/license-mobility/)。 有关详细信息，请参阅 [SQL Server Azure VM 定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)。

1. **如果已通过即用即付库映像之一创建了 VM，是否可以将该 VM 更改为使用自己的 SQL Server 许可证？**

   不是。 无法将按秒付费许可切换为使用自己的许可证。 请使用 [BYOL 映像](virtual-machines-windows-sql-server-iaas-overview.md#BYOL)之一创建新的 Azure 虚拟机，然后使用标准[数据迁移技术](virtual-machines-windows-migrate-sql.md)将数据库迁移到新的服务器。

1. **如果 SQL Server 仅用于待机/故障转移，是否必须付费才能在 Azure VM 上为 SQL Server 授予许可？**

   如果你具有[虚拟机许可常见问题解答](http://azure.microsoft.com/pricing/licensing-faq/)中所述的“软件保障”并且使用“许可证移动性”，则无需付费即可为在 HA 部署中作为被动次要副本参与的 SQL Server 授予许可。 否则，你需要付费才可为其授予许可。


## <a name="administration"></a>管理

1. **是否可以在同一 VM 上安装另一个 SQL Server 实例？是否可以更改默认实例的已安装功能？**

   可以。 SQL Server 安装媒体位于 **C** 驱动器上的某个文件夹中。 可从该位置运行 **Setup.exe** 添加新的 SQL Server 实例，或更改计算机上 SQL Server 的其他已安装功能。 请注意，某些功能（例如自动备份、自动修补和 Azure Key Vault 集成）仅对默认实例起作用。

1. **是否可以卸载 SQL Server 的默认实例？**

   可以，但需注意以下事项。 如前面的解答中所述，依赖于 [SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)的功能仅对默认实例起作用。 卸载默认实例后，该扩展会继续查找默认实例并可能生成事件日志错误。 这些错误来自以下两个来源：**Microsoft SQL Server 凭据管理**和 **Microsoft SQL Server IaaS 代理**。 其中一个错误可能类似于以下内容：

      建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 找不到或无法访问服务器。

   如果你决定卸载默认实例，还要卸载 [SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)。

1. **是否可从 SQL VM 完全删除 SQL Server？**

   是的，但仍将按照 [SQL Server Azure VM 的定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)收取 SQL VM 费用。 如果不再需要 SQL Server，可以部署新的虚拟机并将数据和应用程序迁移到新的虚拟机。 然后可以删除 SQL Server 虚拟机。
   
## <a name="updating-and-patching"></a>更新和修补

1. **如何将 Azure VM 中的 SQL Server 升级到新版本？**

   目前，在 Azure VM 中运行的 SQL Server 不提供任何就地升级。 因此，请使用所需的 SQL Server 版本创建新的 Azure 虚拟机，然后使用标准[数据迁移技术](virtual-machines-windows-migrate-sql.md)，将数据库迁移到新的服务器。

1. **如何将更新和服务包应用到 SQL Server VM？**

   虚拟机允许控制主机，包括应用更新的时间与方法。 对于操作系统，可以手动应用 Windows 更新，或者启用名为[自动修补](virtual-machines-windows-sql-automated-patching.md)的计划服务。 自动修补将安装任何标记为重要的更新，包括该类别中的 SQL Server 更新。 必须手动安装其他可选的 SQL Server 更新。

## <a name="general"></a>常规

1. **Azure VM 是否支持 SQL Server 故障转移群集实例 (FCI)？**

   是的。 可在 [Windows Server 2016 上创建 Windows 故障转移群集 ](virtual-machines-windows-portal-sql-create-failover-cluster.md)，并将存储空间直通 (S2D) 用于群集存储。 或者，可使用第三方群集或存储解决方案，如 [Azure 虚拟机中 SQL Server 的高可用性和灾难恢复](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)中所述。

   > [!IMPORTANT]
   > 目前，Azure 上的 SQL Server FCI 不支持 [SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)。 我们建议你从参与 FCI 的 VM 中卸载扩展。 此扩展支持自动备份和修补等功能，以及 SQL 的一些门户功能。 卸载代理后，这些功能对 SQL VM 不起作用。

1. **SQL VM 与 SQL 数据库服务之间的差别是什么？**

   从概念上讲，在 Azure 虚拟机上运行 SQL Server 与在远程数据中心运行 SQL Server 并没什么不同。 相比之下， [SQL 数据库](../../../sql-database/sql-database-technical-overview.md) 可提供数据库即服务。 使用 SQL 数据库时，无法访问托管数据库的计算机。 有关完整比较，请参阅[选择云 SQL Server 选项：Azure SQL (PaaS) 数据库或 Azure VM 上的 SQL Server (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)。

1. **如何在 Azure VM 上安装 SQL 数据工具？**

    从 [Microsoft SQL Server 数据工具 - Visual Studio 2013 商业智能](https://www.microsoft.com/en-us/download/details.aspx?id=42313)下载并安装 SQL 数据工具。

## <a name="resources"></a>资源

**Windows VM**：

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)。
* [设置 SQL Server Windows VM](virtual-machines-windows-portal-sql-server-provision.md)
* [将数据库迁移到 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)
* [Azure 虚拟机中 SQL Server 的高可用性和灾难恢复](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure 虚拟机中 SQL Server 的性能最佳实践](virtual-machines-windows-sql-performance.md)
* [Azure 虚拟机中的 SQL Server 的应用程序模式和开发策略](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**：

* [Linux VM 上的 SQL Server 概述](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [设置 SQL Server Linux VM](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [常见问题 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [“Linux 上的 SQL Server”文档](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
