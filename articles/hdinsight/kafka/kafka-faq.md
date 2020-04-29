---
title: 有关 Azure HDInsight 中的 Apache Kafka 的常见问题解答
description: 获取有关 Azure HDInsight 上的 Apache Kafka（一个托管式 Hadoop 云服务）的常见问题的解答。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: d27c648980338b3a9e12bd618eb4620fe9988fd7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80436892"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>有关 Azure HDInsight 中的 Apache Kafka 的常见问题解答

本文解答使用 Azure HDInsight 上的 Apache Kafka 时出现的一些常见问题。

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>HDInsight 支持哪些 Kafka 版本？

请在[在 HDInsight 中可以使用哪些 Apache Hadoop 组件和版本？](../hdinsight-component-versioning.md#supported-hdinsight-versions)中查找有关 HDInsight 正式支持的组件版本的详细信息。 建议始终使用最新版本，以确保获得最佳性能和用户体验。

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>HDInsight Kafka 群集中提供哪些资源，我需要为哪些资源付费？

HDInsight Kafka 群集包含以下资源：

* 头节点
* Zookeeper 节点
* 代理（工作器）节点 
* 附加到代理节点的 Azure 托管磁盘
* 网关节点

所有这些资源根据 [HDInsight 定价模型](https://azure.microsoft.com/pricing/details/hdinsight/)收费（网关节点除外）。 无需为网关节点付费。

有关各种节点类型的更详细说明，请参阅 [Azure HDInsight 虚拟网络体系结构](../hdinsight-virtual-network-architecture.md)。 定价基于每分钟节点使用量。 价格因节点大小、节点数、所用托管磁盘类型和区域而异。

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Apache Kafka API 是否适用于 HDInsight？

是，HDInsight 使用本机 Kafka API。 无需更改客户端应用程序代码。 请参阅[教程：使用 Apache Kafka 生成者和使用者 API](./apache-kafka-producer-consumer-api.md) 来了解如何对群集使用基于 Java 的生成者/使用者 API。

## <a name="can-i-change-cluster-configurations"></a>是否可以更改群集配置？

是的，可通过 Ambari 门户更改。 门户中的每个组件都附带一个“配置”部分，可在该部分更改组件配置。  某些更改可能需要重启代理。

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>HDInsight 支持 Apache Kafka 哪种类型的身份验证？

使用[企业安全性套餐（ESP）](../domain-joined/apache-domain-joined-architecture.md)，可以获取 Kafka 群集的主题级安全性。 有关详细信息，请参阅[教程：在 HDInsight 中配置 Apache Kafka 的策略企业安全性套餐（预览）](../domain-joined/apache-domain-joined-run-kafka.md)。

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>我的数据是否已加密？ 是否可以使用我自己的密钥？

托管磁盘上的所有 Kafka 消息都已通过 [Azure 存储服务加密 (SSE)](../../storage/common/storage-service-encryption.md) 进行加密。 传输中的数据（例如，在客户端与代理之间来回传输的数据）默认不会加密。 可以通过[自行设置 TLS](./apache-kafka-ssl-encryption-authentication.md)来加密此类流量。 此外，HDInsight 允许管理其自身的密钥来加密静态数据。 有关详细信息，请参阅[客户托管的密钥磁盘加密](../disk-encryption.md)。

## <a name="how-do-i-connect-clients-to-my-cluster"></a>如何将客户端连接到群集？

要使 Kafka 客户端可与 Kafka 代理通信，这些客户端必须能够通过网络访问代理。 对于 HDInsight 群集而言，虚拟网络 (VNet) 是安全边界。 因此，将客户端连接到 HDInsight 群集的最简单方法是在群集所在的同一 VNet 中创建客户端。 这些方案包括：

* 连接其他 Azure VNet 中的客户端 – 将群集 VNet 对等互连到客户端 VNet，并在群集中配置 [IP 播发](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising)。 使用 IP 播发时，Kafka 客户端必须使用代理 IP 地址而不是完全限定的域名 (FQDN) 来连接代理。

* 连接本地客户端 – 根据[规划 Azure HDInsight 的虚拟网络](../hdinsight-plan-virtual-network-deployment.md)中所述，使用 VPN 网络并设置自定义 DNS 服务器。

* 为 Kafka 服务创建公共终结点 – 如果你的企业安全要求允许这样做，可为 Kafka 代理部署一个公共终结点，或者结合公共终结点部署一个自我管理的开源 REST 终结点。

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>是否可在现有群集中添加更多的磁盘空间？

若要增加 Kafka 消息可用的空间量，可以增大节点数目。 目前不支持将更多磁盘添加到现有群集。

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Kafka 群集是否可与 Databricks 配合使用？ 

是的，只要 Kafka 群集位于同一 VNet 中，就可以使用 Databricks。 若要将 Kafka 群集与 Databricks 配合使用，请在其中创建一个包含 HDInsight Kafka 群集的 VNet，然后在创建 Databricks 工作区并使用 VNet 注入时指定该 VNet。 有关详细信息，请参阅[在 Azure 虚拟网络（VNet 注入）中部署 Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)。 创建 Databricks 工作区时，需要提供 Kafka 群集的启动代理名称。 有关检索 Kafka broker 名称的信息，请参阅[获取 Apache Zookeeper 和 broker 主机信息](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started#getkafkainfo)。

## <a name="how-can-i-have-maximum-data-durability"></a>如何获得最大的数据持久性？

数据持久性可以最大程度地降低消息丢失的风险。 若要实现最大数据持久性，我们建议采用以下设置：

* 在大部分区域中使用最小复制系数 3
* 在仅包含两个容错域的区域中使用最小复制系数 4
* 禁用不纯粹的群首选举
* 将 **min.insync.replicas** 设置为 2 或更大 - 这会更改在继续写入之前必须与群首完全同步的副本数
* 将 **ack** 属性设置为 **all** - 此属性要求所有副本确认所有消息

配置 Kafka 以提高数据一致性的操作会影响生成请求的代理的可用性。

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>是否可将数据复制到多个群集？

是，可以使用 Kafka MirrorMaker 将数据复制到多个群集。 有关设置 MirrorMaker 的详细信息，请参阅[镜像 Apache Kafka 主题](apache-kafka-mirroring.md)。 此外，还有其他自我管理的开源技术（例如 [Brooklin](https://github.com/linkedin/Brooklin/)）和供应商能够帮助你复制到多个群集。

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>是否可以升级群集？ 如何升级群集？

目前不支持就地升级群集版本。 若要将群集更新到更高的 Kafka 版本，请使用所需的版本创建新群集，然后迁移 Kafka 客户端以使用新群集。

## <a name="how-do-i-monitor-my-kafka-cluster"></a>如何实现监视我的 Kafka 群集？

使用 Azure monitor 分析[Kafka 日志](./apache-kafka-log-analytics-operations-management.md)。

## <a name="next-steps"></a>后续步骤

* [在 Azure HDInsight 中为 Apache Kafka 设置 TLS 加密和身份验证](./apache-kafka-ssl-encryption-authentication.md)
* [使用 MirrorMaker 通过 Kafka on HDInsight 复制 Apache Kafka 主题](./apache-kafka-mirroring.md)
