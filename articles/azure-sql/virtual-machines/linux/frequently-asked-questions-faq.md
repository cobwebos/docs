---
title: Linux 上的 SQL Server 虚拟机 FAQ |Microsoft Docs
description: 本文提供有关运行 Linux 上的 SQL Server 虚拟机的常见问题的解答。
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: afd36eb079b6e5f29fa664050cded3c5ddd17d8e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669454"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-virtual-machines"></a>Linux 上的 SQL Server 虚拟机的常见问题
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/frequently-asked-questions-faq.md)
> * [Linux](frequently-asked-questions-faq.md)

本文提供了有关在[虚拟机](sql-server-on-linux-vm-what-is-iaas-overview.md)上运行 Linux 上的 SQL Server 一些最常见问题的解答。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>映像

1. **有哪些 SQL Server 虚拟机库映像可用？**

   Azure 为 Linux 和 Windows 的所有版本中的所有受支持的主要版本 SQL Server 维护虚拟机（VM）映像。 有关更多详细信息，请参阅 [Linux VM 映像](sql-server-on-linux-vm-what-is-iaas-overview.md#create)和 [Windows VM 映像](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)的完整列表。

1. **现有的 SQL Server 虚拟机库映像是否会更新？**

   每隔两个月，都会使用最新的 Linux 和 Windows 更新对虚拟机库中的 SQL Server 映像进行更新。 对于 Linux 映像，这包括最新的系统更新。 对于 Windows 映像，这包括 Windows 更新中标记为重要的所有更新，包括重要的 SQL Server 安全更新和 service pack。 Linux 和 Windows 的 SQL Server 累积更新以不同的方式进行处理。 对于 Linux，SQL Server 累积更新也包含在刷新中。 但目前，Windows VM 不会连同 SQL Server 或 Windows Server 累积更新一起更新。

1. 还会安装哪些相关的 SQL Server 包？

   若要查看默认情况下安装在 Linux 上的 SQL Server Vm 上的 SQL Server 包，请参阅[已安装的包](sql-server-on-linux-vm-what-is-iaas-overview.md#packages)。

1. **是否可以从库中删除 SQL Server 虚拟机映像？**

   是的。 Azure 只为每个主要版本维护一个映像。 例如，发布新的 SQL Server Service Pack 时，Azure 会将新映像添加到该 Service Pack 的库。 先前 Service Pack 的 SQL Server 映像将立即从 Azure 门户中删除。 但是，在接下来的三个月，仍可以通过 PowerShell 预配该映像。 三个月之后，先前的 Service Pack 映像不再可用。 如果 SQL Server 版本由于生命周期结束而不受支持，则也会应用此删除策略。

## <a name="creation"></a>创建

1. **如何实现使用 SQL Server 创建 Linux 虚拟机？**

   最简单的解决方案是创建包含 SQL Server 的 Linux 虚拟机。 有关注册 Azure 并从门户创建 SQL Server VM 的教程，请参阅[在 Azure 门户中预配运行 SQL Server 的 Linux 虚拟机](sql-vm-create-portal-quickstart.md)。 此外，你也可以选用免费许可版（开发人员版或速成版），或通过重新使用本地许可证在 VM 上手动安装 SQL Server。 如果自带许可，必须[在 Azure 上通过软件保障实现许可证移动性](https://azure.microsoft.com/pricing/license-mobility)。

1. 为什么无法使用有支出限制的 Azure 订阅来设置 RHEL 或 SLES SQL Server VM？

   RHEL 和 SLES 虚拟机需要无支出限制的订阅和与订阅相关联的经验证付款方式（通常为信用卡）。 如果你在设置 RHEL 或 SLES VM 时没有删除支出限制，订阅将被禁用，并且所有 VM/服务都将停止。 如果你确实进入了此状态，请重新启用订阅[删除支出限制](https://account.windowsazure.com/subscriptions)。 如果你选择重新启动并继续运行，则剩余信用额度将在当前账单周期恢复，但 RHEL 或 SLES VM 映像附加费将会从你的信用卡中扣除。

## <a name="licensing"></a>授权

1. **如何在 Azure VM 上安装 SQL Server 的许可版本？**

   首先，创建仅限 Linux OS 的虚拟机。 然后，运行适用于 Linux 分发版的 [SQL Server 安装步骤](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms)。 除非你要安装 SQL Server 的一个免费许可版，否则还必须拥有 SQL Server 许可证和[通过 Azure 上的软件保障的许可移动性](https://azure.microsoft.com/pricing/license-mobility/)。

1. 是否有适用于 SQL Server 的自带许可 (BYOL) Linux 虚拟机映像？

   目前，没有适用于 SQL Server 的任何 BYOL Linux 虚拟机映像。 但是，可以在仅限 Linux 的 VM 上手动安装 SQL Server ，如前面问题中所述。

1. **如果已通过即用即付库映像之一创建了 VM，是否可以将该 VM 更改为使用自己的 SQL Server 许可证？**

   不是。 无法将按秒付费许可切换为使用自己的许可证。 你必须创建新的 Linux VM、安装 SQL Server，并迁移数据。 请参阅前面的问题，了解有关使用自己许可证的详细信息。

## <a name="administration"></a>管理

1. **能否使用 SQL Server Management Studio （SSMS）管理运行 SQL Server 的 Linux 虚拟机？**

   是的，但 SSMS 目前是仅限 Windows 的工具。 必须从 Windows 计算机远程连接才能将 SSMS 与运行 SQL Server 的 Linux Vm 配合使用。 在 Linux 本地，新的 [mssql-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) 工具可以执行许多管理任务。 有关跨平台数据库管理工具，请参阅 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is)。

1. **是否可从 SQL Server VM 完全删除 SQL Server？**

   是的，但仍将按照 SQL Server Azure VM 的[定价指南](../windows/pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json)收取 SQL Server VM 费用。 如果不再需要 SQL Server，可以部署新的虚拟机并将数据和应用程序迁移到新的虚拟机。 然后可以删除 SQL Server 虚拟机。

## <a name="updating-and-patching"></a>更新和修补

1. **如何将 Azure VM 中的 SQL Server 升级到新版本？**

   目前，在 Azure VM 中运行的 SQL Server 不提供任何就地升级。 因此，请使用所需的 SQL Server 版本创建新的 Azure 虚拟机，然后使用[标准数据迁移技术](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview)，将数据库迁移到新的服务器。

## <a name="general"></a>常规

1. **Azure Vm 是否支持 SQL Server 高可用性解决方案？**

   现在不行。 Always On 可用性组和故障转移群集都需要 Linux 中的群集解决方案，如 Pacemaker。 SQL Server 支持的 Linux 发行版本不支持云中高可用性加载项。

## <a name="resources"></a>资源

**Linux VM**：

* [Linux VM 上的 SQL Server 概述](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [在 Linux VM 上预配 SQL Server](sql-vm-create-portal-quickstart.md)
* [“Linux 上的 SQL Server”文档](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Windows VM**：

* [Windows VM 上的 SQL Server 概述](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
* [在 Windows VM 上预配 SQL Server](../windows/sql-vm-create-portal-quickstart.md)
* [常见问题 (Windows)](../windows/frequently-asked-questions-faq.md)