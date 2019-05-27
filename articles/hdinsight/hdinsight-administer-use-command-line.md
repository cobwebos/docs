---
title: 管理 Azure HDInsight 群集使用 Azure CLI
description: 了解如何使用 Azure CLI 管理 Azure HDInsight 群集。 群集类型包括 Apache Hadoop、 Spark、 HBase、 Storm、 Kafka、 交互式查询和机器学习服务。
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tyfox
ms.openlocfilehash: 7c12831c43762ddc776e8d5701f002be97992cbc
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65859960"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>管理 Azure HDInsight 群集使用 Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

了解如何使用[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)来管理 Azure HDInsight 群集。 Azure 命令行接口 (CLI) 是用于管理 Azure 资源的 Microsoft 跨平台命令行体验。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

* Azure CLI。 如果你尚未安装 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)的步骤。

* HDInsight 上的 Apache Hadoop 群集。 请参阅 [Linux 上的 HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)。

## <a name="connect-to-azure"></a>连接到 Azure

登录到 Azure 订阅。 如果你打算使用 Azure Cloud Shell，然后只需选择**试试**代码块右上角。 否则，请输入以下命令：

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>列出群集

使用[az hdinsight 列表](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list)列出群集。 编辑以下命令通过将替换为`RESOURCE_GROUP_NAME`与资源组的名称，然后输入命令：

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>显示群集

使用[az hdinsight 显示](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show)以显示在指定的群集的信息。 通过替换来编辑下面的命令`RESOURCE_GROUP_NAME`，和`CLUSTER_NAME`使用相关的信息，然后输入命令：

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>删除群集

使用[az hdinsight 删除](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete)若要删除在指定的群集。 通过替换来编辑下面的命令`RESOURCE_GROUP_NAME`，和`CLUSTER_NAME`使用相关的信息，然后输入命令：

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

还可通过删除包含该群集的资源组来删除群集。 请注意，这将删除包括默认存储帐户的组中的所有资源。

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>缩放群集

使用[az hdinsight 重设大小](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize)调整为指定的大小指定的 HDInsight 群集的大小。 通过替换来编辑下面的命令`RESOURCE_GROUP_NAME`，和`CLUSTER_NAME`包含相关信息。 替换为`TARGET_INSTANCE_COUNT`与所需的群集的辅助角色节点数。 有关缩放群集的详细信息，请参阅[缩放 HDInsight 群集](./hdinsight-scaling-best-practices.md)。 输入以下命令：

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --target-instance-count TARGET_INSTANCE_COUNT
```

## <a name="next-steps"></a>后续步骤

在本文中，已了解如何执行不同的 HDInsight 群集管理任务。 若要了解更多信息，请参阅下列文章：

* [使用 Azure 门户管理 HDInsight 中的 Apache Hadoop 群集](hdinsight-administer-use-portal-linux.md)
* [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)
* [Azure HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Azure CLI 入门](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
