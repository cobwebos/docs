---
title: 如何配置 Azure SQL 数据库托管实例 | Microsoft Docs
description: 了解如何配置和管理 Azure SQL 数据库托管实例。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 886f06e8640891ac09d1e4624335a7bfebcd3def
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60340785"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>如何在 Azure SQL 数据库中使用托管实例

本文提供各种指南、脚本和说明，可帮助你管理和配置托管实例。

## <a name="migration"></a>迁移

- [迁移到托管实例](sql-database-managed-instance-migrate.md) - 了解迁移到托管实例时建议使用的迁移流程和工具。

- [将 TDE 证书迁移到托管实例](sql-database-managed-instance-migrate-tde-certificate.md) - 如果 SQL Server 数据库受透明数据加密 (TDE) 保护，则需迁移证书，以便托管实例能够使用该证书来解密需要在 Azure 中还原的备份。

## <a name="network-configuration"></a>网络配置

- [确定托管实例子网的大小](sql-database-managed-instance-determine-size-vnet-subnet.md) - 托管实例置于专用子网中，在向该子网添加资源以后，就不能重设其大小。 因此，需要根据要部署在子网中的实例的数目和类型来计算子网所需地址的 IP 范围。
- [为托管实例创建新的 VNet 和子网](sql-database-managed-instance-create-vnet-subnet.md) - 必须根据[此处所述的网络要求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)配置要在其中部署托管实例的 Azure VNet 和子网。 本指南介绍如何轻松地创建针对托管实例进行了相应配置的全新 VNet 和子网。
- [为托管实例配置现有的 VNet 和子网](sql-database-managed-instance-configure-vnet-subnet.md) - 如果想要配置现有的 VNet 和子网，以便在其中部署托管实例，可以在此处找到脚本，以便检查[网络要求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)并根据要求进行子网配置。
- [配置自定义 DNS](sql-database-managed-instance-custom-dns.md) - 如果需要通过 DB 邮件配置文件的链接服务器从托管实例访问自定义域上的外部资源，则需配置自定义 DNS。
- [同步网络配置](sql-database-managed-instance-sync-network-configuration.md) - 虽然[已将应用与 Azure 虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md)，仍可能出现无法建立与托管实例的连接的情况。 可尝试刷新服务计划的网络配置。
- [查找管理终结点 IP 地址](sql-database-managed-instance-find-management-endpoint-ip-address.md)– 托管实例出于管理目的使用公共终结点。 可以使用此处所述的脚本确定管理终结点的 IP 地址。
- [验证内置防火墙保护](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) - 托管实例受内置防火墙的保护，该防火墙仅在必需端口上允许流量。 可以使用本指南中介绍的脚本来检查并验证内置防火墙规则。
- [连接应用程序](sql-database-managed-instance-connect-app.md) - 托管实例置于你自己的专用 Azure VNet 中，使用的 IP 地址是专用地址。 了解如何通过不同的模式将应用程序连接到托管实例。

## <a name="feature-configuration"></a>功能配置

- 可以使用[事务复制](replication-with-sql-database-managed-instance.md)在托管实例之间复制数据，或者将数据从本地 SQL Server 复制到托管实例，反之亦然。 详细了解如何使用并配置本指南中的事务复制。
- [配置威胁检测](sql-database-managed-instance-threat-detection.md) - [威胁检测](sql-database-threat-detection-overview.md)是一项内置的 Azure SQL 数据库功能，用于检测各种潜在的攻击，例如 SQL 注入或者来自可疑位置的访问。 本指南介绍如何为托管实例启用并配置[威胁检测](sql-database-threat-detection-overview.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解[单一数据库操作指南](sql-database-howto-single-database.md)