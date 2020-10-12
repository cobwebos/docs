---
title: Azure HDInsight 上的自定义 Apache Ambari 数据库
description: 了解如何创建包含自己的自定义 Apache Ambari 数据库的 HDInsight 群集。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/24/2019
ms.author: hrasheed
ms.openlocfilehash: 1858e06567a0ab0907e6d2cb60358ff4ac00f9a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86086341"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>设置包含自定义 Ambari DB 的 HDInsight 群集

Apache Ambari 简化了 Apache Hadoop 群集的管理和监视。 Ambari 提供易于使用的 Web UI 和 REST API。 Ambari 包括在 HDInsight 群集中，用于监视群集和进行配置更改。

在常规群集创建中，如在 [hdinsight 中设置群集](hdinsight-hadoop-provision-linux-clusters.md)之类的其他文章中所述，Ambari 部署在由 hdinsight 管理并且用户无法访问的 [S0 Azure SQL 数据库](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier) 中。

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
- 必须有现有的 Azure SQL DB 服务器和数据库。
- 为 Ambari 设置提供的数据库必须是空的。 没有任何表采用默认的 dbo 架构。
- 用于连接到数据库的用户应该对该数据库拥有 SELECT、CREATE TABLE 和 INSERT 权限。
- 在托管 Ambari 的服务器上启用“[允许访问的 Azure 服务](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps)”选项。
- 需要在防火墙规则中允许来自 HDInsight 服务的管理 IP 地址。 有关必须添加到服务器级别防火墙规则的 IP 地址列表，请参阅 [HDInsight 管理 IP 地址](hdinsight-management-ip-addresses.md)。

在外部数据库中托管 Apache Ambari DB 时，请记住以下几点：

- 你需要负责支付用于保存 Ambari 的 Azure SQL DB 的额外费用。
- 定期备份自定义 Ambari DB。 Azure SQL 数据库会自动生成备份，但备份保留时间范围有所不同。 有关详细信息，请参阅[了解 SQL 数据库自动备份](../azure-sql/database/automated-backups-overview.md)。

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>部署包含自定义 Ambari DB 的群集

若要创建使用你自己的外部 Ambari 数据库的 HDInsight 群集，请使用[自定义 Ambari DB 快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)。

编辑 `azuredeploy.parameters.json` 中的参数，指定有关新群集以及用于保存 Ambari 的数据库的信息。

可以使用 Azure CLI 开始部署。 请将 `<RESOURCEGROUPNAME>` 替换为要在其中部署群集的资源组。

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>后续步骤

- [使用外部元数据存储 - Azure HDInsight](hdinsight-use-external-metadata-stores.md)
