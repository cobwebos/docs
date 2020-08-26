---
title: 在 Azure HDInsight 上缩放 HiveServer2
description: 使用边缘节点横向缩放 Azure HDInsight 群集上的 HiveServer2，以提高容错能力和可用性。
services: hdinsight
ms.service: hdinsight
ms.topic: conceptual
ms.reviewer: hrasheed-msft
ms.author: kecheung
author: kcheeeung
ms.date: 08/12/2020
ms.openlocfilehash: d0a0df4791492c1c9f0d600630d723024c46c1b8
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227082"
---
# <a name="scale-hiveserver2-on-azure-hdinsight-clusters-for-high-availability"></a>缩放 Azure HDInsight 群集上的 HiveServer2 以实现高可用性

了解如何在群集中部署其他 HiveServer2，以提高可用性和负载分配。 当不需要增加头节点大小时，还可以使用边缘节点来部署 HiveServer2。 

> [!NOTE]
> 根据您的使用情况，增加 HiveServer2 的数量可能会增加与 Hive 元存储的连接数。 还要确保正确调整 Azure SQL 数据库的大小。

## <a name="prerequisites"></a>必备条件

若要使用本指南，你需要了解以下文章：
- [在 HDInsight 中的 Apache Hadoop 群集上使用空边缘节点](hdinsight-apps-use-edge-node.md)

## <a name="install-hiveserver2"></a>安装 HiveServer2

在本部分中，你将在目标主机上安装其他 HiveServer2。

1. 在浏览器中打开 Ambari，并单击目标主机。

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-a.png" alt-text="Ambari 的 "主机" 菜单。":::

2. 单击 "添加" 按钮，然后单击 HiveServer2

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-b.png" alt-text="添加主机的 HiveServer2 面板。":::

3. 确认，进程将运行。 对所有所需主机重复1-3。

4. 完成安装后，请使用陈旧配置重新启动所有服务并启动 HiveServer2。

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-c.png" alt-text="启动 HiveServer2 面板。":::

## <a name="next-steps"></a>后续步骤

本文介绍了如何在群集上安装 HiveServer2。 若要了解有关边缘节点和应用程序的详细信息，请参阅以下文章：

* [安装边缘节点](hdinsight-apps-use-edge-node.md)：了解如何在 HDInsight 群集上安装边缘节点。
* [安装 HDInsight 应用程序](hdinsight-apps-install-applications.md)：了解如何在群集上安装 HDInsight 应用程序。
* [AZURE SQL DTU 连接限制](../azure-sql/database/resource-limits-dtu-single-databases.md)：了解 azure sql 数据库使用 DTU 的限制。
* [AZURE Sql VCore 连接限制](../azure-sql/database/resource-limits-vcore-elastic-pools.md)：了解使用 Vcore 的 azure SQL 数据库限制。
