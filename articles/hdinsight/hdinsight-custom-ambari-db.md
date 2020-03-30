---
title: Azure HDInsight 上的自定义 Apache Ambari 数据库
description: 了解如何使用您自己的自定义 Apache Ambari 数据库创建 HDInsight 群集。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: hrasheed
ms.openlocfilehash: e7351e2f39c7e4eed84f4a47e3eeb2214a062a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240155"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>使用自定义 Ambari 数据库设置 HDInsight 群集

阿帕奇·安巴里简化了阿帕奇哈多普集群的管理和监视。 Ambari 提供了一个易于使用的 Web UI 和 REST API。 Ambari 包含在 HDInsight 群集上，用于监视群集和进行配置更改。

在正常群集创建中，如在[HDInsight 中设置群集](hdinsight-hadoop-provision-linux-clusters.md)等其他文章中所述，Ambari 部署在由 HDInsight 管理的[S0 Azure SQL 数据库中](../sql-database/sql-database-dtu-resource-limits-single-databases.md#standard-service-tier)，用户无法访问。

自定义 Ambari 数据库功能允许您部署新群集，并在您管理的外部数据库中设置 Ambari。 部署是使用 Azure 资源管理器模板完成的。 此功能具有以下优点：

- 自定义 - 选择数据库的大小和处理容量。 如果具有处理密集型工作负载的大型群集，则规格较低的 Ambari 数据库可能成为管理操作的瓶颈。
- 灵活性 - 您可以根据需要扩展数据库，以满足您的要求。
- 控制 - 您可以以符合组织要求的方式管理数据库的备份和安全。

本文的其余部分将讨论以下几点：

- 使用自定义 Ambari DB 功能的要求
- 使用您自己的 Apache Ambari 外部数据库预配 HDInsight 群集所需的步骤

## <a name="custom-ambari-db-requirements"></a>自定义 Ambari 数据库要求

您可以部署具有所有群集类型和版本的自定义 Ambari DB。 多个群集不能使用相同的 Ambari DB。

自定义 Ambari DB 具有以下其他要求：

- 您必须具有现有的 Azure SQL 数据库服务器和数据库。
- 为 Ambari 设置提供的数据库必须为空。 默认 dbo 架构中不应有表。
- 用于连接到数据库的用户应具有数据库的"选择"、"创建表"和"插入"权限。
- 打开"[允许访问](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#azure-portal-steps)Azure SQL 服务器上的 Azure 服务"选项，您将在 Azure SQL 服务器上承载 Ambari。
- 需要允许 SQL 服务器中允许来自 HDInsight 服务的管理 IP 地址。 有关必须添加到 SQL 服务器防火墙的 IP 地址的列表，请参阅[HDInsight 管理 IP 地址](hdinsight-management-ip-addresses.md)。

在外部数据库中托管 Apache Ambari DB 时，请记住以下几点：

- 您负责容纳 Ambari 的 Azure SQL DB 的额外费用。
- 定期备份自定义 Ambari DB。 Azure SQL 数据库会自动生成备份，但备份保留时间范围会有所不同。 有关详细信息，请参阅[了解 SQL 数据库自动备份](../sql-database/sql-database-automated-backups.md)。

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>使用自定义 Ambari DB 部署群集

要创建使用您自己的外部 Ambari 数据库的 HDInsight 群集，请使用[自定义 Ambari DB 快速启动模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)。

编辑 中的参数`azuredeploy.parameters.json`以指定有关新群集和将保存 Ambari 的数据库的信息。

可以使用 Azure CLI 开始部署。 替换为`<RESOURCEGROUPNAME>`要部署群集的资源组。

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>后续步骤

- [使用外部元数据存储 - Azure HDInsight](hdinsight-use-external-metadata-stores.md)