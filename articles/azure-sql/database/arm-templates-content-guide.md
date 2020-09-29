---
title: Azure 资源管理器模板 - Azure SQL 数据库和 SQL 托管实例
description: 使用 Azure 资源管理器模板创建和配置 Azure SQL 数据库和 Azure SQL 托管实例。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 48a2c00f7ff487def13e9872c4f43a3ca36809ba
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91444636"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Azure SQL 数据库和 SQL 托管实例的 Azure 资源管理器模板
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

使用 Azure 资源管理器模板可将基础结构定义为代码，并将解决方案部署到 Azure SQL 数据库和 Azure SQL Managed Instance 的 Azure 云中。

## <a name="azure-sql-database"></a>[Azure SQL 数据库](#tab/single-database)

下表包含 Azure SQL 数据库的 Azure 资源管理器模板链接。

|链接 |说明|
|---|---|
| [SQL 数据库](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | 此 Azure 资源管理器模板在 Azure SQL 数据库中创建单一数据库并配置服务器级 IP 防火墙规则。 |
| [Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | 此 Azure 资源管理器模板为 Azure SQL 数据库创建服务器。 |
| [弹性池](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | 通过此模板可部署弹性池并向其分配数据库。 |
| [故障转移组](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | 此模板在 Azure SQL 数据库中创建两个服务器、一个单一数据库和一个故障转移组。|
| [威胁检测](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | 使用此模板可以部署启用了威胁检测的服务器和一组数据库，并为每个数据库提供用于接收警报的电子邮件地址。 威胁检测是 SQL 高级威胁防护 (ATP) 产品/服务的一部分，它提供一个安全层来应对服务器和数据库受到的潜在威胁。|
| [在 Azure Blob 存储中审核](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | 使用此模板可以部署启用了审核的服务器，以将审核日志写入 Blob 存储。 Azure SQL 数据库审核会跟踪数据库事件，并将这些事件写入到可放入 Azure 存储帐户、OMS 工作区或事件中心的审核日志。|
| [在 Azure 事件中心审核](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | 使用此模板可以部署启用了审核的服务器，以将审核日志写入现有事件中心。 若要将审核事件发送到事件中心，请使用 `Enabled` `State` 指定审核设置，并将 `IsAzureMonitorTargetEnabled` 设置为 `true`。 此外，请使用 `master` 数据库中的 `SQLSecurityAuditEvents` 日志类别配置诊断设置（用于服务器级别审核）。 审核可跟踪数据库事件，并将这些事件写入到可放入 Azure 存储帐户、OMS 工作区或事件中心的审核日志。|
| [使用 SQL 数据库的 Azure Web 应用](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | 此示例在“基本”服务级别创建免费的 Azure Web 应用和 Azure SQL 数据库中的数据库。|
| [使用 SQL 数据库的 Azure Web 应用和 Redis 缓存](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | 此模板在同一资源组中创建 Web 应用、Redis 缓存和数据库，并在适用于数据库和 Redis 缓存的 Web 应用中创建两个连接字符串。|
| [使用 ADF V2 从 Blob 存储导入数据](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | 此 Azure 资源管理器模板创建 Azure 数据工厂 V2 的实例，用于将数据从 Azure Blob 存储复制到 SQL 数据库。|
| [带有数据库的 HDInsight 群集](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | 使用此模板可以创建一个 HDInsight 群集、一个逻辑 SQL 服务器、一个数据库和两个表。 [将 Sqoop 与 HDInsight 中的 Hadoop 配合使用](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop)一文中使用了此模板。 |
| [按计划运行 SQL 存储过程的 Azure 逻辑应用](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | 使用此模板可以创建一个按计划运行 SQL 存储过程的逻辑应用。 可将该过程的所有参数放入该模板的正文部分。|

## <a name="azure-sql-managed-instance"></a>[Azure SQL 托管实例](#tab/managed-instance)

下表包含指向 Azure SQL 托管实例的 Azure 资源管理器模板的链接。

|链接|说明|
|---|---|
| [新 VNet 中的 SQL 托管实例](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | 此 Azure 资源管理器模板在虚拟网络中创建新配置的 Azure 虚拟网络和托管实例。 |
| [SQL 托管实例的网络环境](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | 此部署创建包含两个子网的已配置 Azure 虚拟网络，其中一个子网专用于托管实例，另一个子网用于放置其他资源（例如 VM、应用服务环境，等等）。 此模板将创建经过适当配置的网络环境，可在该环境中部署托管实例。 |
| [使用 P2S 连接的 SQL 托管实例](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | 此部署将创建包含两个子网 `ManagedInstance` 和 `GatewaySubnet` 的 Azure 虚拟网络。 SQL Managed Instance 将部署在 ManagedInstance 子网中。 将在 `GatewaySubnet` 子网中创建虚拟网络网关，并为其配置点到站点 VPN 连接。 |
| [使用虚拟机的 SQL Managed Instance](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | 此部署将创建包含两个子网 `ManagedInstance` 和 `Management` 的 Azure 虚拟网络。 SQL Managed Instance 将部署在 `ManagedInstance` 子网中。 使用最新版 SQL Server Management Studio (SSMS) 的虚拟机将部署在 `Management` 子网中。 |

---

