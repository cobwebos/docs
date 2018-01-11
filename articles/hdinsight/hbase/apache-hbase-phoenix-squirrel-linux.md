---
title: "在 Azure HDInsight 中将 Apache Phoenix 和 SQuirreL 用于 HBase | Microsoft Docs"
description: "了解如何在 HDInsight 中使用 Apache Phoenix。 此外，了解如何在计算机上安装和设置 SQLLine 以连接到 HDInsight 中的 HBase 群集。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: cda0f33b-a2e8-494c-972f-ae0bb482b818
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: 12791da56f32ffffa4b1131c408829f50f6e9124
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2018
---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>将 Apache Phoenix 与 HDinsight 中基于 Linux 的 HBase 群集配合使用
了解如何在 Azure HDInsight 中使用 [Apache Phoenix](http://phoenix.apache.org/)，以及如何使用 SQLLine。 有关 Phoenix 的详细信息，请参阅[在 15 分钟或更短时间内了解 Phoenix](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html)。 有关 Phoenix 语法，请参阅 [Phoenix 语法](http://phoenix.apache.org/language/index.html)。

> [!NOTE]
> 有关 HDInsight 的 Phoenix 版本信息，请参阅 [HDInsight 提供的 Hadoop 群集版本的新增功能](../hdinsight-component-versioning.md)。
>
>

## <a name="use-sqlline"></a>使用 SQLLine
[SQLLine](http://sqlline.sourceforge.net/) 是用于执行 SQL 的命令行实用工具。

### <a name="prerequisites"></a>先决条件
使用 SQLLine 之前，必须先准备好以下各项：

* **HDInsight 中的 HBase 群集**。 若要创建一个 HBase 群集，请参阅 [HDInsight 中的 Apache HBase 入门](./apache-hbase-tutorial-get-started-linux.md)。

在连接到 HBase 群集时，需要连接到 ZooKeeper VM 之一。 每个 HDInsight 群集具有三个 ZooKeeper VM。

获取 ZooKeeper 主机名

1. 通过浏览到 **https://\<群集名称\>.azurehdinsight.net** 打开 Ambari。
2. 要登录，请输入 HTTP（群集）用户名和密码。
3. 在左侧菜单中，选择“ZooKeeper”。 将列出三个 ZooKeeper Server 实例。
4. 选择其中一个 ZooKeeper Server 实例。 在“摘要”窗格中，找到主机名。 它类似于 zk1 jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net。

**使用 SQLLine**

1. 使用 SSH 连接到群集。 有关详细信息，请参阅 [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

2. 在 SSH 中，运行以下命令以运行 SQLLine：

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. 要创建 HBase 表并插入一些数据，请运行以下命令：

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

有关详细信息，请参阅 [SQLLine 手册](http://sqlline.sourceforge.net/#manual)和 [Phoenix 语法](http://phoenix.apache.org/language/index.html)。

## <a name="next-steps"></a>后续步骤
通过本文，你已了解如何在 HDInsight 中使用 Apache Phoenix。 若要了解更多信息，请参阅下列文章：

* [HDInsight HBase 概述][hdinsight-hbase-overview]。
  HBase 是构建于 Hadoop 上的 Apache 开源 NoSQL 数据库，用于为大量非结构化和半结构化数据提供随机访问和高度一致性。
* [在 Azure 虚拟网络上预配 HBase 群集][hdinsight-hbase-provision-vnet]。
  通过虚拟网络集成，可以将 HBase 群集部署到应用程序所在的虚拟网络，以便应用程序直接与 HBase 进行通信。
* [在 HDInsight 中配置 HBase 复制](apache-hbase-replication.md)。 了解如何跨两个 Azure 数据中心设置 HBase 复制。


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


