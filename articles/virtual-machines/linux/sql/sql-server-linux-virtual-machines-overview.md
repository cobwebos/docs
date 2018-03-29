---
title: Azure Linux 虚拟机上的 SQL Server 概述 | Microsoft Docs
description: 了解如何在 Azure Linux 虚拟机上运行完整的 SQL Server 版本。 获取到所有 Linux SQL Server VM 映像和相关内容的直接链接。
services: virtual-machines-linux
documentationcenter: ''
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.workload: iaas-sql-server
ms.date: 03/22/2018
ms.author: jroth
ms.openlocfilehash: e752ad844a6efe572564e7081ebac87193e9c2a7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Azure 虚拟机 (Linux) 上的 SQL Server 概述

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

本主题介绍在 Azure Linux 虚拟机 (VM) 上运行 SQL Server 的选项，提供了[门户映像链接](#create)。

> [!NOTE]
> 如果已熟悉 SQL Server，只想了解如何部署 SQL Server Linux VM，请参阅[在 Azure 中预配 Linux SQL Server VM](provision-sql-server-linux-virtual-machine.md)。 或者，若要创建带 SQL Server 的 Windows VM，请参阅[在 Azure 中预配 Windows SQL Server VM](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)。

如果用户是数据库管理员或开发人员，则可通过 Azure VM 将本地 SQL Server 工作负荷和应用程序移到云中。

## <a name="scenarios"></a>方案

用户选择在 Azure 中托管数据有许多原因。 若要开发应用程序或将其迁移到 Azure，则也可在 Azure 中查找备份数据，从而改进性能。 用户可以自动获得多个数据中心的访问权限，从而获得全局支持和灾难恢复能力。 另外，数据的安全性和持久性也得到了高度保障。

在 Azure VM 中运行 SQL Server 是在 Azure 中存储关系数据的一个选项。 也可使用 Azure SQL 数据库服务。 若要详细了解如何在虚拟机上的 SQL Server 与 Azure SQL 数据库之间进行选择，请参阅[选择云 SQL Server 选项：Azure SQL (PaaS) 数据库或 Azure VM 上的 SQL Server (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)。

## <a id="create"></a>创建新的 SQL VM

在以下教程中查找创建新 SQL VM 的分步指南：[在 Azure 中预配 Linux SQL Server VM](provision-sql-server-linux-virtual-machine.md)。

下表提供了虚拟机库中最新 SQL Server 映像的矩阵。 单击任何链接，即可开始创建具有指定版本和操作系统的新 SQL VM。

> [!TIP]
> 若要了解这些映像的 VM 和 SQL 定价，请参阅 [Linux SQL Server VM 定价页](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。

| 版本 | 操作系统 | 版本 |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> 若要查看可用的 Windows SQL Server 虚拟机映像，请参阅 [Azure 虚拟机上的 SQL Server 概述 (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)。

## <a id="packages"></a> 已安装程序包

在 Linux 上配置 SQL Server 时，请先安装数据库引擎包，然后根据需要安装多个可选包。 用于 SQL Server 的 Linux 虚拟机自动安装大多数包。 下表显示为每个分发安装了哪些包。

| 分发 | [数据库引擎](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [工具](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server 代理](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [全文搜索](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [HA 外接程序](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![是](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![是](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![是](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![是](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![是](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![否](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![是](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![是](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![是](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![是](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![否](./media/sql-server-linux-virtual-machines-overview/no.png) | ![否](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![是](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![是](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![是](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![是](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![是](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![是](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="next-steps"></a>后续步骤

若要详细了解如何在 Linux 上配置和使用 SQL Server，请参阅 [Linux 上的 SQL Server 概述](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)。
