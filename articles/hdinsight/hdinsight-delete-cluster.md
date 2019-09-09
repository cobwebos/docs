---
title: 如何删除 HDInsight 群集 | Azure
description: 有关删除 Azure HDInsight 群集的各种方式的信息
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 1c9214f3084d7b1e2012ec69e3b8e3fe6d08c09e
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810261"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>使用浏览器、PowerShell 或 Azure CLI 删除 HDInsight 群集

HDInsight 群集计费在创建群集之后便会开始，删除群集后才会停止。 群集以每分钟按比例收费，因此无需再使用群集时，应始终将其删除。 在本文档中，了解如何使用[Azure 门户](https://portal.azure.com)、 [Azure PowerShell Az 模块](https://docs.microsoft.com/powershell/azure/overview)和[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)删除群集。

> [!IMPORTANT]  
> 删除 HDInsight 群集时不会删除与群集关联的 Azure 存储帐户或 Data Lake Storage。 可重新使用以后存储在这些服务中的数据。

## <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在左侧菜单中，导航到“所有服务” > “分析” > “HDInsight 群集”，然后选择群集。

3. 在默认视图中，选择“删除”图标。 按提示删除群集。
   
    ![删除图标](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell-az-module"></a>Azure PowerShell Az 模块

在下面的代码中，将 `CLUSTERNAME` 替换为 HDInsight 群集的名称。 在 PowerShell 提示符处输入以下命令，以便删除群集：

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

在下面的代码中，将 `CLUSTERNAME` 替换为 HDInsight 群集的名称，将 `RESOURCEGROUP` 替换为资源组的名称。  在命令提示符处输入以下命令，以便删除群集：

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
