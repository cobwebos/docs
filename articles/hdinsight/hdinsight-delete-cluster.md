---
title: "如何删除 HDInsight 群集 | Microsoft Docs"
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
ms.date: 10/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 67442bf4b04f6f3799d30f7ce26547c8145d9168


---
# <a name="how-to-delete-an-hdinsight-cluster"></a>如何删除 HDInsight 群集
群集创建后，HDInsight 群集将立即启动；群集删除后，HDInsight 群集则立即停止，且 HDInsight 群集每分钟按比例收费，因此你始终应在不再使用群集时将其删除。 在本文档中，将学习如何使用 Azure 门户、Azure PowerShell 和 Azure CLI 删除群集。

> [!IMPORTANT]
> 删除 HDInsight 群集不会删除与群集关联的 Azure 存储帐户。 这样你就可以保留和重复使用由群集存储的任何数据。
> 
> 

## <a name="azure-portal"></a>Azure 门户
1. 登录到 [Azure 门户](https://portal.azure.com)，并选择你的 HDInsight 群集。 如果你的 HDInsight 群集没有固定到仪表板，则可使用导航栏右侧的搜索字段（放大镜图标）按名称进行搜索。
   
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




<!--HONumber=Nov16_HO3-->


