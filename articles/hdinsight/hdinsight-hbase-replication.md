---
title: "在 Azure 虚拟网络中设置 HBase 群集复制 | Microsoft Docs"
description: "了解如何设置从一个 HDInsight 版本到另一个版本的 HBase 复制，以实现负载均衡、高可用性、在不造成停机的情况下进行迁移和更新，以及灾难恢复。"
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 87f4d219a678cab78980af2ff8b98ae55739f04c
ms.contentlocale: zh-cn
ms.lasthandoff: 09/09/2017

---
# <a name="set-up-hbase-cluster-replication-in-azure-virtual-networks"></a>在 Azure 虚拟网络中设置 HBase 群集复制

了解如何在 Azure 中的一个虚拟网络内部或者在两个虚拟网络之间设置 HBase 复制。

群集复制使用源推送方法。 HBase 群集可以是一个源或一个目标，也可以同时充当这两个角色。 复制是异步的。 复制的目标是保持最终一致性。 在启用复制的情况下，当源接收到对列系列的编辑时，该编辑将传播到所有目标群集。 当数据从一个群集复制到另一个群集，会跟踪源群集和所有已使用数据的群集，防止复制循环。

本教程介绍如何设置源-目标复制。 对于其他群集拓扑，请参阅 [Apache HBase 参考指南](http://hbase.apache.org/book.html#_cluster_replication)。

下面是单个虚拟网络的 HBase 复制用例：

* 负载均衡。 例如，可以在目标群集上运行扫描或 MapReduce 作业，在源群集上引入数据。
* 添加高可用性。
* 在不同的 HBase 群集之间迁移数据。
* 将 Azure HDInsight 群集从一个版本升级到另一个版本。

下面是两个虚拟网络的 HBase 复制用例：

* 设置灾难恢复。
* 对应用程序进行负载均衡和分区。
* 添加高可用性。

可以使用 [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication) 中的[脚本操作](hdinsight-hadoop-customize-cluster-linux.md)脚本复制群集。

## <a name="prerequisites"></a>先决条件
在开始学习本教程之前，必须有一个 Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

## <a name="set-up-the-environments"></a>设置环境

有三个配置选项：

- 两个 HBase 群集位于一个 Azure 虚拟网络中。
- 两个 HBase 群集位于同一区域的两个不同虚拟网络中。
- 两个 HBase 群集位于两个不同区域的两个不同虚拟网络中（异地复制）。

为了帮助设置环境，我们创建了一些 [Azure 资源管理器模板](../azure-resource-manager/resource-group-overview.md)。 如果想要使用其他方法设置环境，请参阅：

- [在 HDInsight 中创建基于 Linux 的 Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)
- [在 Azure 虚拟网络中创建 HBase 群集](hdinsight-hbase-provision-vnet.md)

### <a name="set-up-one-virtual-network"></a>设置一个虚拟网络

若要在同一虚拟网络中创建两个 HBase 群集，请选择下图。 模板存储在 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/)中。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="set-up-two-virtual-networks-in-the-same-region"></a>在同一区域中设置两个虚拟网络

若要在同一区域中创建两个使用虚拟网络对等互连的虚拟网络和两个 HBase 群集，请选择下图。 模板存储在 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/)中。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



此方案需要[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)。 模板启用虚拟网络对等互连。   

HBase 复制使用 ZooKeeper VM 的 IP 地址。 必须为目标 HBase ZooKeeper 节点设置静态 IP 地址。

**配置静态 IP 地址**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单上，选择“资源组”。
3. 选择包含目标 HBase 群集的资源组。 这是使用 Resource Manager 模板创建环境时指定的资源组。 可以使用筛选器减少列表中的结果。 可以看到包含两个虚拟网络的资源的列表。
4. 选择包含目标 HBase 群集的虚拟网络。 例如，选择 **xxxx-vnet2**。 会列出名称以 **nic-zookeepermode-** 开头的三个设备。 这些设备是三个 ZooKeeper VM。
5. 选择其中一个 ZooKeeper VM。
6. 选择“IP 配置”。
7. 在列表中，选择 **ipConfig1**。
8. 选择“静态”，复制或记下实际 IP 地址。 运行脚本操作启用复制时，需要用到该 IP 地址。

  ![HDInsight HBase 复制 ZooKeeper 静态 IP 地址](./media/hdinsight-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. 重复步骤 6，为另外两个 ZooKeeper 节点设置静态 IP 地址。

对于跨虚拟网络方案，调用 `hdi_enable_replication.sh` 脚本操作时必须使用 **-ip** 开关。

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>在两个不同的区域中设置两个虚拟网络

若要在两个不同的区域中创建两个虚拟网络，请选择下图。 模板存储在全局 Azure Blob 容器中。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fdeploy-hbase-geo-replication.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

在两个虚拟网络之间创建 VPN 网关。 有关说明，请参阅[创建使用站点到站点连接的虚拟网络](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)。

HBase 复制使用 ZooKeeper VM 的 IP 地址。 必须为目标 HBase ZooKeeper 节点设置静态 IP 地址。 若要设置静态 IP，请参阅本文的[在同一区域中设置两个虚拟网络](#set-up-two-virtual-networks-in-the-same-region)。

对于跨虚拟网络方案，调用 `hdi_enable_replication.sh` 脚本操作时必须使用 **-ip** 开关。

## <a name="load-test-data"></a>加载测试数据

复制群集时，必须指定要复制的表。 在本节中，要将一些数据载入源群集。 在下一部分，会在两个群集之间启用复制。

若要创建一个“联系人”表并在其中插入一些数据，请遵照 [HBase 教程：开始在 HDInsight 中将 Apache HBase 与基于 Linux 的 Hadoop 配合使用](hdinsight-hbase-tutorial-get-started-linux.md)中的说明。

## <a name="enable-replication"></a>启用复制

以下步骤说明如何从 Azure 门户调用脚本操作脚本。 有关使用 Azure PowerShell 和 Azure 命令行工具 (Azure CLI) 运行脚本操作的信息，请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。

**从 Azure 门户启用 HBase 复制**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 打开源 HBase 群集。
3. 在群集菜单中，选择“脚本操作”。
4. 在页面顶部，选择“提交新项”。
5. 选择或输入以下信息：

  1. **名称**：输入“启用复制”。
  2. **Bash 脚本 URL**：输入 **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**。
  3.  **头**：确保已选定。 清除其他节点类型。
  4. **参数**：以下示例参数将对所有现有表启用复制，并将源群集中的所有数据复制到目标群集：

            -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata

6. 选择“创建” 。 该脚本可能会运行一段时间，尤其是在使用 **-copydata** 参数的情况下。

必需参数：

|名称|说明|
|----|-----------|
|-s、--src-cluster | 指定源 HBase 群集的 DNS 名称。 例如：-s hbsrccluster、--src-cluster=hbsrccluster |
|-d、--dst-cluster | 指定目标（副本）HBase 群集的 DNS 名称。 例如：-s dsthbcluster、--src-cluster=dsthbcluster |
|-sp、--src-ambari-password | 指定源 HBase 群集的 Ambari 管理员密码。 |
|-dp、--dst-ambari-password | 指定目标 HBase 群集的 Ambari 管理员密码。|

可选参数：

|名称|说明|
|----|-----------|
|-su、--src-ambari-user | 指定源 HBase 群集的 Ambari 管理员用户名。 默认值为 **admin**。 |
|-du、--dst-ambari-user | 指定目标 HBase 群集的 Ambari 管理员用户名。 默认值为 **admin**。 |
|-t、--table-list | 指定要复制的表。 例如：--table-list="table1;table2;table3"。 如果不指定表，将复制所有现有的 HBase 表。|
|-m、--machine | 指定要在其中运行脚本操作的头节点。 值为 **hn1** 或 **hn0**。 由于 **hn0** 头节点通常较为繁忙，因此我们建议使用 **hn1**。 在 HDInsight 门户或 Azure PowerShell 中以脚本操作的形式运行 $0 脚本时，可使用此选项。|
|-ip | 在两个虚拟网络之间启用复制时，需使用此参数。 此参数充当一个开关，它使用副本群集中 ZooKeeper 节点的静态 IP 地址而不是 FQDN 名称。 在启用复制之前，需要预先配置静态 IP 地址。 |
|-cp、-copydata | 在启用复制的情况下，允许迁移表中的现有数据。 |
|-rpm、-replicate-phoenix-meta | 针对 Phoenix 系统表启用复制。 <br><br>*请谨慎使用此选项。* 建议在使用此脚本之前，在副本群集上重新创建 Phoenix 表。 |
|-h、--help | 显示用法信息。 |

该[脚本](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh)的 `print_usage()` 节中提供了详细的参数说明。

成功部署脚本操作后，可以使用 SSH 连接到目标 HBase 群集，并验证是否已复制数据。

### <a name="replication-scenarios"></a>复制方案

以下列表显示了一些普通用例及其参数设置：

- **针对两个群集之间的所有表启用复制**。 此方案不需要复制或迁移表中的现有数据，也不使用 Phoenix 表。 使用以下参数：

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **针对特定的表启用复制**。 若要针对 table1、table2 和 table3 启用复制，请使用以下参数：

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **针对特定的表启用复制并复制现有数据**。 若要针对 table1、table2 和 table3 启用复制，请使用以下参数：

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **针对所有表启用复制，并将 Phoenix 元数据从源复制到目标**。 Phoenix 元数据复制并不完善， 请谨慎使用。 使用以下参数：

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>复制并迁移数据

启用复制后，可以使用两个单独的脚本操作脚本来复制或迁移数据：

- [针对小型表的脚本](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh)（大小只有几个 GB，有望在不到 1 小时内完成整个复制的表）

- [针对大型表的脚本](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh)（预计需要一小时以上才能完成复制的表）

可以遵循[启用复制](#enable-replication)中所述的相同过程来调用脚本操作。 使用以下参数：

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

该[脚本](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh)的 `print_usage()` 节中提供了详细的参数说明。

### <a name="scenarios"></a>方案

- **复制特定表（test1、test2 和 test3）中到目前（当前时间戳）为止编辑的所有行**：

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  或：

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **复制指定时间范围内的特定表**：

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>禁用复制

若要禁用复制，可以使用 [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) 中的另一个脚本操作脚本。 可以遵循[启用复制](#enable-replication)中所述的相同过程来调用脚本操作。 使用以下参数：

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

该[脚本](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh)的 `print_usage()` 节中提供了详细的参数说明。

### <a name="scenarios"></a>方案

- **对所有表禁用复制**：

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  或

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **对指定的表（table1、table2 和 table3）禁用复制**：

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>后续步骤

本教程已介绍如何在一个虚拟网络内部或者在两个虚拟网络之间设置 HBase 复制。 若要了解有关 HDInsight 和 HBase 的详细信息，请参阅以下文章：

* [开始在 HDInsight 中使用 Apache HBase][hdinsight-hbase-get-started]
* [HDInsight HBase 概述][hdinsight-hbase-overview]
* [在 Azure 虚拟网络中创建 HBase 群集][hdinsight-hbase-provision-vnet]
* [使用 HBase 分析实时 Twitter 观点][hdinsight-hbase-twitter-sentiment]
* [使用 HDInsight (Hadoop) 中的 Storm 和 HBase 分析传感器数据][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started-linux.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

