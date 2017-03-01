---
title: "在 HDInsight 中创建基于 Windows 的 Hadoop 群集 | Microsoft Docs"
description: "了解如何使用 Azure 门户创建 HDInsight 中基于 Windows 的 Hadoop。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c8689ef3-f56f-4708-8a3a-cc00abc54e8d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/18/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: ede2e4ec5f3414d1c8a17f4c120011eba0d9a6ca
ms.openlocfilehash: a9d7a4372693ad021a898fae9818f2d037d42547


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-the-azure-portal"></a>使用 Azure 门户在 HDInsight 中创建基于 Windows 的 Hadoop 群集

[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

了解如何使用 Azure 门户创建 HDInsight 中基于 Windows 的 Hadoop 群集。 

本文中的信息仅适用于基于 Windows 的 HDInsight 群集。 有关创建基于 Linux 的群集的信息，请参阅[使用 Azure 门户创建 HDInsight 中的 Hadoop 群集](hdinsight-hadoop-create-linux-clusters-portal.md)。

> [!IMPORTANT]
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight Deprecation on Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)（HDInsight 在 Windows 上即将弃用）。

## <a name="prerequisites"></a>先决条件：
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

在开始按照本文中的说明操作之前，你必须具有以下内容：

* Azure 订阅。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

### <a name="access-control-requirements"></a>访问控制要求
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>创建群集
**创建 HDInsight 群集**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 依次单击“新建”、“智能 + 分析”、“HDInsight”。
3. 键入或选择以下值：
   
   * **群集名称**：为群集命名。
   * **订阅**：选择用于此群集的 Azure 订阅。
   * **群集配置**：

     * **群集类型**：对于本教程，选择“Hadoop”。
     * **操作系统**：对于本教程，选择“Windows”。
     * **版本**：选择 **Hadoop 2.7.0 (HDI 3.3)**。  这是基于 Windows 的 Hadoop 群集的最新版本。
     * **群集层**：对于本教程，选择“标准”。

   * **凭据**：

     * **群集登录用户名**：默认名称为**管理员**。 
     * **群集登录密码**：输入密码。
     * **启用远程桌面**：在本教程中，无需使用远程桌面。

   * **数据源**：

     * **选择方法**：选择“来自所有订阅”。
     * **选择存储帐户**：单击“新建”，然后为默认存储帐户输入名称。
     * **默认容器**：默认情况下，默认容器使用与群集相同的名称。
     * **位置**：选择离你近的位置。  群集和默认存储帐户将共同使用此位置。
   * **群集大小**：

     * **辅助角色节点数**：本教程仅需要 1 个节点。
   * **高级配置**：对本教程而言，你可以跳过此部分。
   * **资源组**：选择“新建”并输入名称。

4. 选择“固定到仪表板”，然后单击“创建”。 选择“固定到启动板”会将群集的磁贴添加到门户的启动板。 创建群集将耗时 15 到 20 分钟。使用启动板上的磁贴或页面左侧的“通知”条目检查预配进程。
5. 创建完成后，在启动板中单击群集磁贴，以启动群集边栏选项卡。 群集边栏选项卡提供有关该群集的基本信息，如名称、其所属的资源组、位置、操作系统、群集仪表板 URL 等。

## <a name="customize-clusters"></a>自定义群集
* 请参阅[使用 Bootstrap 自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-bootstrap.md)。
* 请参阅[使用脚本操作自定义基于 Windows 的 HDInsight 群集](hdinsight-hadoop-customize-cluster.md)。

## <a name="next-steps"></a>后续步骤
在本文中，你已经学习了几种创建 HDInsight 群集的方法。 若要了解更多信息，请参阅下列文章：

* [Azure HDInsight 入门](hdinsight-hadoop-linux-tutorial-get-started.md) - 了解如何开始使用 HDInsight 群集
* [以编程方式提交 Hadoop 作业](hdinsight-submit-hadoop-jobs-programmatically.md) - 了解如何以编程方式将作业提交到 HDInsight
* [使用 Azure 门户管理 HDInsight 中的 Hadoop 群集](hdinsight-administer-use-management-portal.md)




<!--HONumber=Jan17_HO3-->


