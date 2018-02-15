---
title: "使用 Azure 数据库迁移服务的先决条件概述 | Microsoft Docs"
description: "了解使用 Azure 数据库迁移服务执行数据库迁移的先决条件的概述。"
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: a103bb4802bc4a20b6d82f0c6bb427133d9e5643
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>使用 Azure 数据库迁移服务的先决条件概述
若要确保 Azure 数据库迁移服务在执行数据库迁移时顺利运行，需要具备几个先决条件。 某些先决条件适用于该服务支持的所有方案（源/目标对），而其他先决条件则是特定方案所独有的。

以下各部分列出了与使用 Azure 数据库迁移服务关联的先决条件。

## <a name="prerequisites-common-across-migration-scenarios"></a>在不同迁移方案中通用的先决条件
在所有支持的迁移方案中通用的 Azure 数据库迁移服务先决条件包括需要：
- 使用 Azure 资源管理器部署模型创建 Azure 数据库迁移服务的 VNET，它将使用 [ExpressRoute](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways) 为本地源服务器提供站点到站点连接。
- 确保 Azure 虚拟网络 (VNET) 网络安全组规则未阻止通信端口 443、53、9354、445、12000。 有关 Azure VNET NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg)一文。
- 在源数据库的前面使用了防火墙设备时，可能需要添加防火墙规则以允许 Azure 数据库迁移服务访问要迁移的源数据库。

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>将 SQL Server 迁移到 Azure SQL 数据库的先决条件 
除了普遍适用于所有迁移方案的 Azure 数据库迁移服务先决条件外，还有特别适用于个别方案的先决条件。

使用 Azure 数据库迁移服务执行 SQL Server 到 Azure SQL 数据库的迁移时，除了普遍适用于所有迁移方案的先决条件外，还必须满足以下附加先决条件：
- 配置[针对数据库引擎访问的 Windows 防火墙](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
- 按照[启用或禁用服务器网络协议](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文中的说明启用 TCP/IP 协议（在安装 SQL Server Express 时，会默认禁用它）。
- 按照[在 Azure 门户中创建 Azure SQL 数据库](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-portal)一文中的详细信息创建 Azure SQL 数据库实例。
- 下载并安装[数据迁移助手](https://www.microsoft.com/en-us/download/details.aspx?id=53595) v3.3 或更高版本。
- 打开 Windows 防火墙，以允许 Azure 数据库迁移服务访问源数据库。
- 为 Azure SQL 数据库服务器创建服务器级[防火墙规则](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure)，以允许 Azure 数据库迁移服务访问目标数据库。 提供用于 Azure 数据库迁移服务的 VNET 子网范围。
- 确保用于连接到源 SQL Server 实例的凭据具有 [CONTROL SERVER](https://docs.microsoft.com/en-us/sql/t-sql/statements/grant-server-permissions-transact-sql) 权限。
- 确保用于连接到目标 Azure SQL 数据库实例的凭据具有目标 Azure SQL 数据库的 CONTROL DATABASE 权限。

   > [!NOTE]
   > 有关使用 Azure 数据库迁移服务执行从 SQL Server 到 Azure SQL 数据库的迁移所需的先决条件的完整列表，请参阅教程[将 SQL Server 迁移到 Azure SQL 数据库](https://docs.microsoft.com/en-us/azure/dms/tutorial-sql-server-to-azure-sql)。
   > 

## <a name="next-steps"></a>后续步骤
有关公共预览版期间 Azure 数据库迁移服务和区域可用性的概述，请参阅[什么是 Azure 数据库迁移服务预览版](dms-overview.md)一文。 