---
title: 如何删除 HDInsight 群集 | Azure
description: 删除 HDInsight 群集的各种方式的相关信息。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: eca7b4f8bd7e91bc8dcb9bcc49ed3b981010aaee
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721015"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>使用浏览器、PowerShell 或 Azure CLI 删除 HDInsight 群集

HDInsight 群集计费在创建群集之后便会开始，删除群集后才会停止。 群集以每分钟按比例收费，因此无需再使用群集时，应始终将其删除。 在本文档中，了解如何删除群集使用[Azure 门户](https://portal.azure.com)， [Azure PowerShell Az 模块](https://docs.microsoft.com/powershell/azure/overview)，并[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

> [!IMPORTANT]  
> 删除 HDInsight 群集时不会删除与群集关联的 Azure 存储帐户或 Data Lake Storage。 可重新使用以后存储在这些服务中的数据。

## <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 从左侧菜单中，导航到**所有服务** > **Analytics** > **HDInsight 群集**和选择你的群集。

3. 从默认视图中，选择**删除**图标。 按照提示操作以删除群集。
   
    ![删除图标](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell-az-module"></a>Azure PowerShell Az module

替换为`CLUSTERNAME`下面的代码中的 HDInsight 群集的名称。 从 PowerShell 提示符下输入以下命令删除群集：

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

替换`CLUSTERNAME`与 HDInsight 群集的名称和`RESOURCEGROUP`下面的代码中的资源组的名称。  从命令提示符下输入以下命令以删除群集：

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
