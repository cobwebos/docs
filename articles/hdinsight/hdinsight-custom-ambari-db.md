---
title: Azure HDInsight 上的自定义 Apache Ambari 数据库
description: 了解如何创建包含自己的自定义 Apache Ambari 数据库的 HDInsight 群集。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: hrasheed
ms.openlocfilehash: e92b0679111a6d5c6173da04c5061c95956125b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322958"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>设置包含自定义 Ambari DB 的 HDInsight 群集

Apache Ambari 简化了 Apache Hadoop 群集的管理和监视。 Ambari 提供易于使用的 Web UI 和 REST API。 Ambari 包括在 HDInsight 群集中，用于监视群集和进行配置更改。

在常规群集创建中，如在[hdinsight 中设置群集](hdinsight-hadoop-provision-linux-clusters.md)之类的其他文章中所述，Ambari 部署在由 hdinsight 管理并且用户无法访问的[S0 Azure SQL 数据库](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier)中。

使用自定义 Ambari DB 功能，可以在由你管理的外部数据库中部署新群集和设置 Ambari。 部署是使用 Azure 资源管理器模板完成的。 此功能提供以下优势：

- 自定义 - 可以选择数据库的大小和处理容量。 如果大型群集需要处理密集型工作负荷，规格较低的 Ambari 数据库可能会成为管理操作的瓶颈。
- 灵活性 - 可根据要求按需缩放数据库。
- 控制 - 可以根据组织的要求管理数据库的备份和安全性。

本文的余下内容将讨论以下几点：

- 使用自定义 Ambari DB 功能的要求
- 使用自己的外部数据库为 Apache Ambari 预配 HDInsight 群集所要执行的步骤

## <a name="custom-ambari-db-requirements"></a>自定义 Ambari DB 的要求

可以使用所有群集类型和版本来部署自定义的 Ambari DB。 多个群集不能使用同一个 Ambari DB。

自定义 Ambari DB 具有以下附加要求：

- 数据库名称不能包含连字符或空格
- 你必须具有现有的 Azure SQL DB 服务器和数据库。
- 为 Ambari 安装程序提供的数据库必须为空。 默认 dbo 架构中不应有任何表。
- 用于连接到数据库的用户应对数据库具有 SELECT、CREATE TABLE 和 INSERT 权限。
- 启用此选项以允许在将托管 Ambari 的服务器上[访问 Azure 服务](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps)。
- 需要在防火墙规则中允许来自 HDInsight 服务的管理 IP 地址。 有关必须添加到服务器级防火墙规则的 IP 地址的列表，请参阅[HDInsight 管理 ip 地址](hdinsight-management-ip-addresses.md)。

在外部数据库中托管 Apache Ambari DB 时，请记住以下几点：

- 你需要负责保存 Ambari 的 Azure SQL DB 的额外成本。
- 定期备份自定义 Ambari 数据库。 Azure SQL 数据库自动生成备份，但备份保留时间框架各不相同。 有关详细信息，请参阅[了解 SQL 数据库自动备份](../azure-sql/database/automated-backups-overview.md)。

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>使用自定义 Ambari DB 部署群集

若要创建使用自己的外部 Ambari 数据库的 HDInsight 群集，请使用[自定义 AMBARI DB 快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)。

编辑中的参数 `azuredeploy.parameters.json` ，指定有关新群集和将保留 Ambari 的数据库的信息。

您可以使用 Azure CLI 开始部署。 将替换 `<RESOURCEGROUPNAME>` 为要在其中部署群集的资源组。

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>后续步骤

- [使用外部元数据存储 - Azure HDInsight](hdinsight-use-external-metadata-stores.md)
