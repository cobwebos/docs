---
title: Azure HDInsight 上的自定义 Apache Ambari 数据库
description: 了解如何创建包含你自己的自定义 Apache Ambari 数据库的 HDInsight 群集。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: hrasheed
ms.openlocfilehash: 8064fd5369e55ea223a697d30d7643ff5407cf76
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065998"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>使用自定义 Ambari DB 设置 HDInsight 群集

Apache Ambari 简化了 Apache Hadoop 群集的管理和监视。 Ambari 提供易于使用的 web UI 和 REST API。 Ambari 包含在 HDInsight 群集上，用于监视群集和进行配置更改。

在常规群集创建中，如在[hdinsight 中设置群集](hdinsight-hadoop-provision-linux-clusters.md)之类的其他文章中所述，Ambari 部署在由 hdinsight 管理并且用户无法访问的[S0 Azure SQL 数据库](../sql-database/sql-database-dtu-resource-limits-single-databases.md#standard-service-tier)中。

使用自定义 Ambari DB 功能，你可以部署新的群集，并在你管理的外部数据库中设置 Ambari。 部署是使用 Azure 资源管理器模板来完成的。 此功能具有以下优点：

- 自定义-选择数据库的大小和处理容量。 如果有大量的群集处理密集型工作负荷，具有较低规范的 Ambari 数据库可能会成为管理操作的瓶颈。
- 灵活性-可以根据需要缩放数据库以满足你的要求。
- 控制-您可以采用适合于组织需求的方式管理数据库的备份和安全。

本文的其余部分将讨论以下几点：

- 使用自定义 Ambari DB 功能的要求
- 为 Apache Ambari 使用自己的外部数据库预配 HDInsight 群集所需的步骤

## <a name="custom-ambari-db-requirements"></a>自定义 Ambari DB 要求

你可以使用所有群集类型和版本部署自定义的 Ambari DB。 多个群集不能使用相同的 Ambari DB。

自定义 Ambari DB 具有以下其他要求：

- 你必须具有现有的 Azure SQL DB 服务器和数据库。
- 为 Ambari 安装程序提供的数据库必须为空。 默认 dbo 架构中不应有任何表。
- 用于连接到数据库的用户应对数据库具有 SELECT、CREATE TABLE 和 INSERT 权限。
- 启用此选项以允许在将托管 Ambari 的 Azure SQL server 上[访问 azure 服务](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#azure-portal-steps)。
- 需要在 SQL Server 中允许来自 HDInsight 服务的管理 IP 地址。 有关必须添加到 SQL server 防火墙的 IP 地址的列表，请参阅[HDInsight 管理 ip 地址](hdinsight-management-ip-addresses.md)。

在外部数据库中托管 Apache Ambari DB 时，请记住以下几点：

- 你需要负责保存 Ambari 的 Azure SQL DB 的额外成本。
- 定期备份自定义 Ambari 数据库。 Azure SQL 数据库自动生成备份，但备份保留时间框架各不相同。 有关详细信息，请参阅[了解 SQL 数据库自动备份](../sql-database/sql-database-automated-backups.md)。

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>使用自定义 Ambari DB 部署群集

若要创建使用自己的外部 Ambari 数据库的 HDInsight 群集，请使用[自定义 AMBARI DB 快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)。

编辑 `azuredeploy.parameters.json` 中的参数，指定有关新群集和将保留 Ambari 的数据库的信息。

您可以使用 Azure CLI 开始部署。 将 `<RESOURCEGROUPNAME>` 替换为要在其中部署群集的资源组。

```azure-cli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>后续步骤

- [在 Azure HDInsight 中使用外部元数据存储](hdinsight-use-external-metadata-stores.md)