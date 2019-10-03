---
title: Azure HDInsight 中的 Apache Kafka 常见问题
description: 获取有关 Azure HDInsight (一种托管 Hadoop 云服务) 上 Apache Kafka 的常见问题的解答。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 057c77d4ddb4a760e196c0dc8d508efe15e6699d
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520122"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>有关 Azure HDInsight 中 Apache Kafka 的常见问题

本文介绍有关在 Azure HDInsight 上使用 Apache Kafka 的一些常见问题解答。

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>HDInsight 支持哪些 Kafka 版本？

有关 hdinsight 的详细信息, 请查看 HDInsight 中[的 Apache Hadoop 组件和版本是什么？](../hdinsight-component-versioning.md#supported-hdinsight-versions)。 建议始终使用最新版本, 以确保最佳的性能和用户体验。

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>HDInsight Kafka 群集中提供了哪些资源, 哪些资源收费？

HDInsight Kafka 群集包括以下资源:

* 头节点
* Zookeeper 节点
* Broker (worker) 节点 
* 附加到代理节点的 Azure 托管磁盘
* 网关节点

所有这些资源都是根据[HDInsight 定价模型](https://azure.microsoft.com/pricing/details/hdinsight/)收费的, 网关节点除外。 你不需要为网关节点付费。

有关各种节点类型的更详细说明, 请参阅[Azure HDInsight 虚拟网络体系结构](../hdinsight-virtual-network-architecture.md)。 定价基于每分钟节点使用量。 价格因节点大小、节点数、所用的托管磁盘类型和区域而异。

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Apache Kafka Api 与 HDInsight 配合使用吗？

是, HDInsight 使用本机 Kafka Api。 你的客户端应用程序代码不需要更改。 有关分步说明，请参阅[教程：使用 Apache Kafka 制造者和使用者](./apache-kafka-producer-consumer-api.md) api 来了解如何在群集中使用基于 Java 的生成者/使用者 api。

## <a name="can-i-change-cluster-configurations"></a>是否可以更改群集配置？

是, 通过 Ambari 门户。 门户中的每个组件都有一个配置部分, 可用于更改组件配置。 某些更改可能需要重新启动代理。

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>HDInsight 支持 Apache Kafka 哪种类型的身份验证？

使用[企业安全性套餐 (ESP)](../domain-joined/apache-domain-joined-architecture.md), 可以获取 Kafka 群集的主题级安全性。 有关分步说明，请参阅[教程：有关详细信息, 请在 HDInsight 中配置企业安全性套餐](../domain-joined/apache-domain-joined-run-kafka.md)(预览版) Apache Kafka 策略。

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>数据是否已加密？ 我可以使用我自己的密钥吗？

托管磁盘上的所有 Kafka 消息都用[Azure 存储服务加密 (SSE)](../../storage/common/storage-service-encryption.md)进行加密。 默认情况下, 数据传输 (例如, 从客户端传输到代理的数据以及其他方法) 不加密。 可以通过[自行设置 SSL](./apache-kafka-ssl-encryption-authentication.md)来加密此类流量。 此外, HDInsight 还允许您管理自己的密钥来加密静态数据。 有关详细信息, 请参阅[在 Azure HDInsight 上 Apache Kafka 自带密钥](apache-kafka-byok.md)。

## <a name="how-do-i-connect-clients-to-my-cluster"></a>如何实现将客户端连接到群集？

要使 Kafka 客户端与 Kafka 代理通信, 这些客户端必须能够通过网络访问代理。 对于 HDInsight 群集, 虚拟网络 (VNet) 是安全边界。 因此, 将客户端连接到 HDInsight 群集的最简单方法是在与群集相同的 VNet 中创建客户端。 其他方案包括:

* 将其他 Azure VNet 中的客户端连接到群集 VNet 和客户端 VNet, 并为[IP 播发](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising)配置群集。 使用 IP 广告时, Kafka 客户端必须使用 Broker IP 地址来连接代理, 而不是完全限定的域名 (Fqdn)。

* 连接本地客户端–使用 VPN 网络并设置自定义 DNS 服务器, 如[规划 Azure HDInsight 的虚拟网络](../hdinsight-plan-virtual-network-deployment.md)中所述。

* 为 Kafka 服务创建公共终结点–如果企业安全要求允许, 则可以为 Kafka 代理或具有公共终结点的自我托管的开源 REST 终结点部署一个公共终结点。

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>能否在现有群集上添加更多的磁盘空间？

若要增加可用于 Kafka 消息的空间量, 可以增加节点数。 目前, 不支持将更多磁盘添加到现有群集。

## <a name="how-can-i-have-maximum-data-durability"></a>如何实现最大的数据持续性？

利用数据持久性, 你可以实现最小的消息丢失风险。 为了实现最大程度的数据持续性, 建议采用以下设置:

* 在大多数区域中使用最小复制系数3
* 在仅有两个容错域的区域中使用最小复制系数4
* 禁用 unclean 导引选举
* 将**最小值 insync**设置为2或更大值-这将更改必须与领导完全同步的副本数, 然后才能继续写入
* 将**ack**属性设置为**all** -此属性要求所有副本都确认所有消息

配置 Kafka 以实现较高的数据一致性会影响代理生成请求的可用性。

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>能否将我的数据复制到多个群集？

是的, 可以使用 Kafka MirrorMaker 将数据复制到多个群集。 有关设置 MirrorMaker 的详细信息, 请参阅[Mirror Apache Kafka 主题](apache-kafka-mirroring.md)。 此外, 还有其他可帮助实现与多个群集 (如[Brooklin](https://github.com/linkedin/Brooklin/)) 的复制的自托管开源技术和供应商。

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>我能否升级我的群集？ 我应该如何升级我的群集？

目前尚不支持就地群集版本升级。 若要将群集更新到较高版本的 Kafka, 请使用所需的版本创建新群集, 并将 Kafka 客户端迁移到使用新群集。

## <a name="how-do-i-monitor-my-kafka-cluster"></a>如何实现监视我的 Kafka 群集？

使用 Azure monitor 分析[Kafka 日志](./apache-kafka-log-analytics-operations-management.md)。

## <a name="next-steps"></a>后续步骤

* [设置 Azure HDInsight 中 Apache Kafka 的安全套接字层 (SSL) 加密和身份验证](./apache-kafka-ssl-encryption-authentication.md)
* [使用 MirrorMaker 通过 Kafka on HDInsight 复制 Apache Kafka 主题](./apache-kafka-mirroring.md)
