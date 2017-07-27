---
title: "如何删除 HDInsight 群集 - Azure | Microsoft Docs"
description: "删除 HDInsight 群集的各种方式的相关信息。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 55f7838b-9786-47ff-96db-1b64437bd0bb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: 7d3aed71d9b5867a87954a60b1e470def0563f71
ms.contentlocale: zh-cn
ms.lasthandoff: 06/13/2017

---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>使用浏览器、PowerShell 或 Azure CLI 删除 HDInsight 群集

创建群集后便开始 HDInsight 群集计费，删除群集后停止计费。 群集以每分钟按比例收费，因此无需再使用群集时，应始终将其删除。 本文档介绍如何使用 Azure 门户、Azure PowerShell 和 Azure CLI 1.0 删除群集。

> [!IMPORTANT]
> 删除 HDInsight 群集时不会删除与群集关联的 Azure 存储帐户。 这使你可以在将来重复使用数据。

## <a name="azure-portal"></a>Azure 门户

1. 登录 [Azure 门户](https://portal.azure.com)，并选择你的 HDInsight 群集。 如果 HDInsight 群集未固定到仪表板，可使用搜索字段按名称进行搜索。
   
    ![门户搜索](./media/hdinsight-delete-cluster/navbar.png)

2. 群集的边栏选项卡打开后，选择“删除”图标。 出现提示时，选择“是”即可删除该群集。
   
    ![删除图标](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

在 PowerShell 提示符处，使用以下命令删除群集：

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

将 **CLUSTERNAME** 替换为 HDInsight 群集名。

## <a name="azure-cli"></a>Azure CLI

在提示符处，使用以下命令删除群集：

    azure hdinsight cluster delete CLUSTERNAME

将 **CLUSTERNAME** 替换为 HDInsight 群集名。


