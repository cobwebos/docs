---
title: SQL 数据库的 Azure 资源管理器模板 | Microsoft Docs
description: 使用 Azure 资源管理器模板创建和配置 Azure SQL 数据库。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 81f3e4beb29f21f1b752a876827e9b35856c713a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566892"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Azure SQL 数据库的 Azure 资源管理器模板

使用 Azure 资源管理器模板可将基础结构定义为代码，并将解决方案部署到 Azure 云。

## <a name="single-database--elastic-pool"></a>单一数据库和弹性池

下表包含 Azure SQL 数据库的 Azure 资源管理器模板链接。

| |  |
|---|---|
| [单个数据库](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | 此 Azure 资源管理器模板创建包含逻辑服务器的单一 Azure SQL 数据库并配置防火墙规则。 |
| [逻辑服务器](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | 此 Azure 资源管理器模板创建 Azure SQL 数据库的逻辑服务器。 |
| [弹性池](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | 使用此模板可以部署新的弹性池，并在其中分配新的关联 SQL Server 和新的 SQL 数据库。 |
| [故障转移组](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | 此模板创建两个 Azure SQL 逻辑服务器、一个 SQL 数据库和一个故障转移组。|
| [威胁检测](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | 使用此模板可以部署启用了威胁检测的 Azure SQL 逻辑服务器和一组 Azure SQL 数据库。对于每个数据库，需要提供用于接收警报的电子邮件地址。 威胁检测是 SQL 高级威胁防护 (ATP) 产品/服务的一部分，它提供一个安全层来应对 SQL 服务器和数据库受到的潜在威胁。|
| [在 Azure Blob 存储中审核](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | 使用此模板可以部署启用了审核的 Azure SQL 逻辑服务器，以将审核日志写入 Blob 存储。 Azure SQL 数据库审核会跟踪数据库事件，并将这些事件写入到可放入 Azure 存储帐户、OMS 工作区或事件中心的审核日志。|
| [在 Azure 事件中心审核](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | 使用此模板可以部署启用了审核的 Azure SQL 服务器，以将审核日志写入现有的事件中心。 若要将审核事件发送到事件中心，请使用 `Enabled` `State` 指定审核设置，并将 `IsAzureMonitorTargetEnabled` 设置为 `true`。 此外，请在 `master` 数据库中使用 `SQLSecurityAuditEvents` 诊断日志类别配置诊断设置（以实现服务器级审核）。 Azure SQL 数据库和 SQL 数据仓库审核会跟踪数据库事件，并将这些事件写入到可放入 Azure 存储帐户、OMS 工作区或事件中心的审核日志。|
| [使用 SQL 数据库的 Azure Web 应用](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | 此示例在“基本”服务级别创建免费的 Azure Web 应用和 SQL 数据库。|
| [使用 SQL 数据库的 Azure Web 应用和 Redis 缓存](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | 此模板在同一资源组中创建 Web 应用、Redis 缓存和 SQL 数据库，并在适用于 SQL 数据库和 Redis 缓存的 Web 应用中创建两个连接字符串。|
| [使用 ADF V2 从 Blob 存储导入数据](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | 此 Azure 资源管理器模板创建 Azure 数据工厂 V2，用于将数据从 Azure Blob 存储复制到 SQL 数据库。|
| [使用 SQL 数据库的 HDInsight 群集](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | 使用此模板可以创建 HDInsight 群集、SQL 数据库服务器、SQL 数据库和两个表。 [将 Sqoop 与 HDInsight 中的 Hadoop 配合使用](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop)一文中使用了此模板 |
| [按计划运行 SQL 存储过程的 Azure 逻辑应用](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | 使用此模板可以创建一个按计划运行 SQL 存储过程的逻辑应用。 可将该过程的所有参数放入该模板的正文部分。|

## <a name="managed-instance"></a>托管实例

下表包含 Azure SQL 数据库 - 托管实例的 Azure 资源管理器模板链接。

| |  |
|---|---|
| [新 VNet 中的托管实例](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | 此 Azure 资源管理器模板在 VNet 中创建新配置的 Azure VNet 和托管实例。 |
| [托管实例的网络环境](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | 此部署创建包含两个子网的已配置 Azure 虚拟网络 - 其中一个子网专用于托管实例，另一个子网用于放置其他资源（例如 VM、应用服务环境，等等）。 此模板将创建经过适当配置的网络环境，可在该环境中部署托管实例。 |
| [使用 P2S 连接的托管实例](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | 此部署将创建包含子网 `ManagedInstance` 和 `GatewaySubnet` 的 Azure 虚拟网络。 托管实例将部署在 ManagedInstance 子网中。 将在 `GatewaySubnet` 子网中创建虚拟网络网关，并为其配置点到站点 VPN 连接。 |
| [使用虚拟机的托管实例](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | 此部署将创建包含子网 `ManagedInstance` 和 `Management` 的 Azure 虚拟网络。 托管实例将部署在 `ManagedInstance` 子网中。 使用最新版 SQL Server Management Studio (SSMS) 的虚拟机将部署在 `Management` 子网中。 |
