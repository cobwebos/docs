---
title: 基础结构：本地 Apache Hadoop 到 Azure HDInsight
description: 了解有关将本地 Hadoop 群集迁移到 Azure HDInsight 的基础结构最佳做法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 8407aafdb9b9dadcbc8e220ac42e5d7856116959
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996779"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>将本地 Apache Hadoop 群集迁移到 Azure HDInsight - 基础结构最佳做法

本文提供有关管理 Azure HDInsight 群集基础结构的建议。 本文是帮助用户将本地 Apache Hadoop 系统迁移到 Azure HDInsight 的最佳做法系列教程中的其中一篇。

## <a name="plan-for-hdinsight-cluster-capacity"></a>规划 HDInsight 群集容量

在 HDInsight 群集容量规划方面要做出的重要选择如下：

**区域**  
Azure 区域确定群集的物理预配位置。 为了将读写延迟最小化，群集应与数据位于同一区域。

**存储位置和大小**  
默认存储必须位于群集所在区域中。 对于 48 节点群集，建议创建 4 到 8 个存储帐户。 尽管存储总量可能已足够，但每个存储帐户能够为计算节点提供额外的网络带宽。 如果有多个存储帐户，请为每个存储帐户使用不带前缀的随机名称。 使用随机名称的目的是降低出现存储瓶颈（限制）或所有帐户发生共模故障的可能性。 为提高性能，请对每个存储帐户仅使用一个容器。

**VM 大小和类型（现在支持 G 系列）**  
每个群集类型具有一组节点类型，每个节点类型在 VM 大小和类型方面提供特定的选项。 VM 大小和类型由 CPU 处理能力、RAM 大小和网络延迟决定。 可以使用模拟工作负荷来确定每个节点类型的最佳 VM 大小和类型。

**工作器节点数**  
可以使用模拟工作负荷来确定初始的工作器节点数。 以后可以通过添加更多工作节点来扩展群集，以满足峰值负载需求。 以后不需要额外的工作器节点时，可以重新缩放群集。

有关详细信息，请参阅有关[HDInsight 群集容量规划](../hdinsight-capacity-planning.md)的文章。

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>对群集使用建议的虚拟机类型

有关每种 HDInsight 群集的建议虚拟机类型，请参阅[群集的默认节点配置和虚拟机大小](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters)。

## <a name="check-hadoop-components-availability-in-hdinsight"></a>检查 HDInsight 中 Hadoop 组件的可用性

每个 HDInsight 版本都是一组 Hadoop 生态系统组件的云分发版。 有关所有 HDInsight 组件及其最新版本的详细信息，请参阅 [HDInsight 组件版本控制](../hdinsight-component-versioning.md)。

还可以使用 Apache Ambari UI 或 Ambari REST API 来检查 HDInsight 中的 Hadoop 组件和版本。

本地群集中提供但不是 HDInsight 群集的一部分的应用程序或组件可以添加到与 HDInsight 群集位于同一 VNet 中的边缘节点或 VM 上。 可以在 HDInsight 群集中使用“应用程序”选项，来安装 Azure HDInsight 中未提供的第三方 Hadoop 应用程序。 可以使用“脚本操作”在 HDInsight 群集上安装自定义的 Hadoop 应用程序。 下表列出了一些常见的应用程序及其 HDInsight 集成选项：

|**应用程序**|**集成**
|---|---|
|气流|IaaS 或 HDInsight 边缘节点
|Alluxio|IaaS  
|Arcadia|IaaS 
|Atlas|无（仅限 HDP）
|Datameer|HDInsight 边缘节点
|Datastax (Cassandra)|IaaS（CosmosDB，Azure 上的替代产品）
|DataTorrent|IaaS 
|钻取|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS（CosmosDB，Azure 上的替代产品）
|NiFi|IaaS 
|Presto|IaaS 或 HDInsight 边缘节点
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS（SQLDW，Azure 上的替代产品）
|Tableau|IaaS 
|Waterline|HDInsight 边缘节点
|StreamSets|HDInsight 边缘 
|Palantir|IaaS 
|Sailpoint|Iaas 

有关详细信息，请参阅[随不同 HDInsight 版本提供的 Apache Hadoop 组件](../hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions)一文

## <a name="customize-hdinsight-clusters-using-script-actions"></a>使用脚本操作自定义 HDInsight 群集

HDInsight 提供名为“脚本操作”的群集配置方法。  脚本操作是一个 Bash 脚本，在 HDInsight 群集中的节点上运行，可用于安装附加的组件和更改配置设置。

必须将脚本操作存储在可从 HDInsight 群集访问的 URI 上。 在创建群集期间或之后可以使用脚本操作，也可以将它们限制为只能在特定的节点类型上运行。

该脚本可以持久保留，或执行一次。 持久化脚本用于自定义通过缩放操作添加到群集的新工作节点。 进行缩放操作时，持久化脚本还可以将更改应用于其他节点类型，如头节点。

HDInsight 提供预先编写的脚本用于在 HDInsight 群集上安装以下组件：

- 添加 Azure 存储帐户
- 安装 Hue
- 安装 Presto
- 安装 Solr
- 安装 Giraph
- 预加载 Hive 库
- 安装或更新 Mono

> [!Note]  
> HDInsight 不直接支持自定义 Hadoop 组件或使用脚本操作安装的组件。

还可以将脚本操作作为 HDInsight 应用程序发布到 Azure 市场。

有关详细信息，请参阅以下文章：

- [在 HDInsight 上安装第三方 Apache Hadoop 应用程序](../hdinsight-apps-install-applications.md)
- [使用脚本操作自定义 HDInsight 群集](../hdinsight-hadoop-customize-cluster-linux.md)
- [在 Azure 市场中发布 HDInsight 应用程序](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>使用 Bootstrap 自定义 HDInsight 配置

可以使用 Bootstrap 对 `core-site.xml`、`hive-site.xml` 和 `oozie-env.xml` 等配置文件中的配置进行更改。 以下脚本是使用 PowerShell [AZ module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) cmdlet [AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster)的示例：

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzHDInsightClusterConfig '
    | Set—AzHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

有关详细信息，请参阅 [使用 Bootstrap 自定义 HDInsight 群集](../hdinsight-hadoop-customize-cluster-bootstrap.md)一文。  另请参阅 [使用 Apache Ambari REST API 管理 HDInsight 群集](../hdinsight-hadoop-manage-ambari-rest-api.md)。

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>从 HDInsight Hadoop 群集边缘节点访问客户端工具

空边缘节点是安装并配置了与头节点中相同的客户端工具，但未运行 Hadoop 服务的 Linux 虚拟机。 边缘节点可用于以下目的：

- 访问群集
- 测试客户端应用程序
- 托管客户端应用程序

可以通过 Azure 门户创建和删除边缘节点，可以在创建群集期间或之后使用边缘节点。 创建边缘节点后，可以使用 SSH 连接到该节点，运行客户端工具访问 HDInsight 中的 Hadoop 群集。 边缘节点 SSH 终结点为 `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`。


有关详细信息，请参阅[在 HDInsight 中的 Apache Hadoop 群集上使用空边缘节点](../hdinsight-apps-use-edge-node.md)一文。


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>使用群集的纵向扩展和缩减功能

HDInsight 提供弹性，可让你选择扩展和缩减群集中的工作节点数。 使用此功能可在若干小时后或者在周末收缩群集，或者在业务高峰期扩展群集。    有关详细信息，请参阅：

* [缩放 HDInsight 群集](../hdinsight-scaling-best-practices.md)。
* [缩放群集](../hdinsight-administer-use-portal-linux.md#scale-clusters)。

## <a name="use-hdinsight-with-azure-virtual-network"></a>通过 Azure 虚拟网络使用 HDInsight

Azure 虚拟网络可以筛选和路由网络流量，使 Azure 资源（例如 Azure 虚拟机）能够以安全方式相互通信，以及与 Internet 和本地网络通信。

对 HDInsight 使用 Azure 虚拟网络可实现以下方案：

- 直接从本地网络连接到 HDInsight。
- 将 HDInsight 连接到 Azure 虚拟网络中的数据存储。
- 直接访问无法通过 Internet 公开访问的 Hadoop 服务。 例如，Kafka API 或 HBase Java API。

可将 HDInsight 添加到新的或现有的 Azure 虚拟网络。 如果将 HDInsight 添加到现有的虚拟网络，则需要更新现有的网络安全组和用户定义的路由，以便能够不受限制地访问 Azure 数据中心内的[多个 IP 地址](../hdinsight-management-ip-addresses.md)。 此外，请确保不要阻止对 HDInsight 服务使用的[端口](../control-network-traffic.md#required-ports)的流量。

> [!Note]  
> HDInsight 目前不支持强制隧道。 强制隧道是一种子网设置，将出站 Internet 流量强制定向到设备以进行检查和记录。 在将 HDInsight 安装到子网之前删除强制隧道，或者为 HDInsight 创建新的子网。 此外，HDInsight 不支持限制出站网络连接。

有关详细信息，请参阅以下文章：

- [Azure 虚拟网络概述](../../virtual-network/virtual-networks-overview.md)
- [使用 Azure 虚拟网络扩展 Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>使用 Azure 虚拟网络服务终结点安全地连接到 Azure 服务

HDInsight 支持[虚拟网络服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)，使你能够安全地连接到 Azure Blob 存储、Azure Data Lake Storage Gen2、COSMOS DB 和 SQL 数据库。 为 Azure HDInsight 启用服务终结点后，流量将通过 Azure 数据中心内部的受保护路由传送。 在网络层实施这种增强的安全级别后，可将大数据存储帐户锁定到其指定的虚拟网络 (VNET)，同时仍可以顺畅地使用 HDInsight 群集来访问和处理这些数据。

有关详细信息，请参阅以下文章：

- [虚拟网络服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [使用服务终结点增强 HDInsight 安全性](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>将 HDInsight 连接到本地网络

可以使用 Azure 虚拟网络和 VPN 网关将 HDInsight 连接到本地网络。 可使用以下步骤建立连接：

- 在已连接到本地网络的 Azure 虚拟网络中使用 HDInsight。
- 配置虚拟网络与本地网络之间的 DNS 名称解析。
- 配置网络安全组或用户定义的路由 (UDR) 来控制网络流量。

有关详细信息，请参阅[将 HDInsight 连接到本地网络](../connect-on-premises-network.md)一文

## <a name="next-steps"></a>后续步骤

阅读本系列文章中的下一篇文章：[本地到 Azure HDInsight Hadoop 迁移的存储最佳实践](apache-hadoop-on-premises-migration-best-practices-storage.md)。
