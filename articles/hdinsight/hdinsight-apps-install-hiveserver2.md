---
title: 在 Azure HDInsight 上缩放 HiveServer2
description: 使用边缘节点在 Azure HDInsight 群集上横向缩放 HiveServer2，以提升容错能力和可用性。
services: hdinsight
ms.service: hdinsight
ms.topic: conceptual
ms.reviewer: hrasheed-msft
ms.author: kecheung
author: kcheeeung
ms.date: 08/12/2020
ms.openlocfilehash: d0a0df4791492c1c9f0d600630d723024c46c1b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227082"
---
# <a name="scale-hiveserver2-on-azure-hdinsight-clusters-for-high-availability"></a>在 Azure HDInsight 群集上缩放 HiveServer2 以实现高可用性

了解如何在群集中另外部署一个 HiveServer2，以提升可用性和负载分配。 当不需要增加头节点的大小时，也可以使用边缘节点来部署 HiveServer2。 

> [!NOTE]
> 根据使用情况，增加 HiveServer2 的数量可能会增加 Hive 元存储连接的数量。 另外请确保 Azure SQL 数据库大小适当。

## <a name="prerequisites"></a>先决条件

若要使用本指南，需要了解以下文章：
- [在 HDInsight 中的 Apache Hadoop 群集上使用空边缘节点](hdinsight-apps-use-edge-node.md)

## <a name="install-hiveserver2"></a>安装 HiveServer2

在本部分中，你将在目标主机上另外安装一个 HiveServer2。

1. 在浏览器中打开 Ambari，并单击目标主机。

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-a.png" alt-text="Ambari 的“主机”菜单。":::

2. 单击“添加”按钮，然后单击“HiveServer2”

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-b.png" alt-text="Ambari 的“主机”菜单。":::

3. 确认，然后该进程将会运行。 对所有需要的主机重复步骤 1-3。

4. 完成安装后，请使用过时配置重新启动所有服务并启动 HiveServer2。

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-c.png" alt-text="Ambari 的“主机”菜单。":::

## <a name="next-steps"></a>后续步骤

本文介绍了如何在群集上安装 HiveServer2。 若要详细了解边缘节点和应用程序，请参阅以下文章：

* [安装边缘节点](hdinsight-apps-use-edge-node.md)：了解如何在 HDInsight 群集上安装边缘节点。
* [安装 HDInsight 应用程序](hdinsight-apps-install-applications.md)：了解如何在群集上安装 HDInsight 应用程序。
* [Azure SQL DTU 连接限制](../azure-sql/database/resource-limits-dtu-single-databases.md)：了解使用 DTU 时的 Azure SQL 数据库限制。
* [Azure SQL vCore 连接限制](../azure-sql/database/resource-limits-vcore-elastic-pools.md)：了解使用 vCore 时的 Azure SQL 数据库限制。
