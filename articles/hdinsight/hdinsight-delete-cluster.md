---
title: 如何删除 HDInsight 群集 | Azure
description: 有关删除 Azure HDInsight 群集的各种方法的信息
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 2912777c001a36ec913e2cfd618091ada5aa107a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807130"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>使用浏览器、PowerShell 或 Azure CLI 删除 HDInsight 群集

创建群集后便开始 HDInsight 群集计费，删除群集后停止计费。 计费是每分钟按比例计费的，因此，当群集不再使用时，应始终将其删除。 在本文档中，您将了解如何使用[Azure 门户](https://portal.azure.com)[、Azure PowerShell Az 模块](https://docs.microsoft.com/powershell/azure/overview)和 Azure [CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)删除群集。

> [!IMPORTANT]  
> 删除 HDInsight 群集时不会删除与群集关联的 Azure 存储帐户或 Data Lake Storage。 可重新使用以后存储在这些服务中的数据。

## <a name="azure-portal"></a>Azure 门户

1. 登录到 Azure[门户](https://portal.azure.com)。

2. 从左侧菜单导航到分析**Analytics** > **HDInsight 群集****的所有服务** > 并选择群集。

3. 在默认视图中，选择“删除”**** 图标。 按提示删除群集。

    ![HDInsight 删除群集按钮](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

在下面的代码中，将 `CLUSTERNAME` 替换为 HDInsight 群集的名称。 在 PowerShell 提示符处输入以下命令，以便删除群集：

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

在下面的代码中，将 `CLUSTERNAME` 替换为 HDInsight 群集的名称，将 `RESOURCEGROUP` 替换为资源组的名称。  在命令提示符处输入以下命令，以便删除群集：

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
