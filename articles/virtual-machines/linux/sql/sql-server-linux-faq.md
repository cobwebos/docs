---
title: "Linux Azure 虚拟机中的 SQL Server 常见问题 | Microsoft 文档"
description: "本文提供有关在 Linux Azure VM 上运行 SQL Server 时遇到的常见问题解答。"
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 10/05/2017
ms.author: jroth
ms.openlocfilehash: a001ae116e33e0b7be4431b0bc4c8bb319f4e801
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>在 Linux Azure 虚拟机上运行 SQL Server 的常见问题

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

本主题提供有关在 [Linux Azure 虚拟机上运行 SQL Server](sql-server-linux-virtual-machines-overview.md) 时出现的一些最常见问题解答。

> [!NOTE]
> 本主题重点介绍在 Linux VM 上运行 SQL Server 的特定问题。 如果你当前在 Windows VM 上运行 SQL Server，请参阅 [Windows 常见问题](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>常见问题

1. 如何创建装有 SQL Server 的 Linux Azure 虚拟机？

   最简单的解决方法是创建包含 SQL Server 的 Linux 虚拟机。 有关注册 Azure 并从门户创建 SQL VM 的教程，请参阅[在 Azure 门户中预配 Linux SQL Server 虚拟机](provision-sql-server-linux-virtual-machine.md)。 此外，你也可以选用免费许可版（开发人员版或速成版），或通过重新使用本地许可证在 VM 上手动安装 SQL Server。 如果自带许可，必须[在 Azure 上通过软件保障实现许可证移动性](https://azure.microsoft.com/pricing/license-mobility)。

1. **如何将 Azure VM 中的 SQL Server 升级到新版本？**

   目前，在 Azure VM 中运行的 SQL Server 不提供任何就地升级。 因此，请使用所需的 SQL Server 版本创建新的 Azure 虚拟机，然后使用[标准数据迁移技术](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview)，将数据库迁移到新的服务器。

1. **如何在 Azure VM 上安装 SQL Server 的许可版本？**

   首先，创建仅限 Linux OS 的虚拟机。 然后，运行适用于 Linux 分发版的 [SQL Server 安装步骤](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms)。 除非你要安装 SQL Server 的一个免费许可版，否则还必须拥有 SQL Server 许可证和[通过 Azure 上的软件保障的许可移动性](https://azure.microsoft.com/pricing/license-mobility/)。

1. 是否有适用于 SQL Server 的自带许可 (BYOL) Linux 虚拟机映像？

   目前，没有适用于 SQL Server 的任何 BYOL Linux 虚拟机映像。 但是，可以在仅限 Linux 的 VM 上手动安装 SQL Server ，如前面问题中所述。

1. **如果已通过即用即付库映像之一创建了 VM，是否可以将该 VM 更改为使用自己的 SQL Server 许可证？**

   不能。 无法将按分钟付费许可切换为使用自己的许可证。 你必须创建新的 Linux VM、安装 SQL Server，并迁移数据。 请参阅前面的问题，了解有关使用自己许可证的详细信息。

1. 还会安装哪些相关的 SQL Server 包？

   若要查看默认情况下在 SQL Server Linux VM 上安装的 SQL Server 包，请参阅[安装的程序包](sql-server-linux-virtual-machines-overview.md#packages)。

1. Azure VM 上是否支持 SQL Server 的高可用性解决方案？

   现在不行。 Always On 可用性组和故障转移群集都需要 Linux 中的聚类分析解决方案，如 Pacemaker。 SQL Server 受支持的 Linux 分发版在云中不支持其高可用性加载项。

1. 为什么无法使用有支出限制的 Azure 订阅来设置 RHEL 或 SLES SQL Server VM？

   RHEL 和 SLES 虚拟机需要无支出限制的订阅和与订阅相关联的经验证付款方式（通常为信用卡）。 如果你在设置 RHEL 或 SLES VM 时没有删除支出限制，订阅将被禁用，并且所有 VM/服务都将停止。 如果你确实进入了此状态，请重新启用订阅[删除支出限制](https://account.windowsazure.com/subscriptions)。 如果你选择重新启动并继续运行，则剩余信用额度将在当前账单周期恢复，但 RHEL 或 SLES VM 映像附加费将会从你的信用卡中扣除。

## <a name="resources"></a>资源

**Linux VM**：

* [Linux VM 上的 SQL Server 概述](sql-server-linux-virtual-machines-overview.md)
* [设置 SQL Server Linux VM](provision-sql-server-linux-virtual-machine.md)
* [“Linux 上的 SQL Server”文档](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Windows VM**：

* [Windows VM 上的 SQL Server 概述](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [设置 SQL Server Windows VM](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [常见问题 (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)