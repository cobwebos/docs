---
title: 在 Azure HDInsight 中控制网络流量
description: 了解控制 Azure HDInsight 群集的入站和出站流量的方法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 929956d6f439df2a2e7cb8d1b950f5e68cdeab68
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631709"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>在 Azure HDInsight 中控制网络流量

可以使用以下方法控制 Azure 虚拟网络中的网络流量：

* **** 网络安全组 (NSG)：用于筛选网络的入站和出站流量。 有关详细信息，请参阅[使用网络安全组筛选网络流量](../virtual-network/security-overview.md)文档。

* **网络虚拟设备** (NVA) 只能用于出站流量。 NVA 可复制设备（如防火墙和路由器）的功能。 有关详细信息，请参阅[网络设备](https://azure.microsoft.com/solutions/network-appliances)文档。

作为托管服务，HDInsight 需要对 HDInsight 运行状况和管理服务具有不受限制的访问权限，以处理从 VNET 传入和传出的流量。 使用 NSG 时，必须确保这些服务仍然可以与 HDInsight 群集进行通信。

![在 Azure 自定义 VNET 中创建的 HDInsight 实体示意图](./media/control-network-traffic/hdinsight-vnet-diagram.png)

## <a name="hdinsight-with-network-security-groups"></a>使用网络安全组的 HDInsight

如果计划使用**网络安全组**来控制网络流量，请在安装 HDInsight 之前执行以下操作：

1. 确定计划用于 HDInsight 的 Azure 区域。

2. 确定 HDInsight 需要的用于你所在区域的服务标记。 有多种方法可以获取这些服务标记：
    1. 请参阅 [Azure HDInsight 的网络安全组 (NSG) 服务标记](hdinsight-service-tags.md)中的已发布服务标记列表。 
    2. 如果你的区域未出现在列表中，请使用[服务标记发现 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) 查找你所在区域的服务标记。
    3. 如果无法使用该 API，请下载[服务标记 JSON 文件](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)并搜索所需的区域。


3. 为计划将 HDInsight 安装到其中的子网创建或修改网络安全组。

    * ____ 网络安全组：在端口 443 上允许来自 IP 地址的入站流量。____ ____ 这将确保 HDInsight 管理服务可从虚拟网络外部访问群集。 对于启用了 Kafka REST 代理的群集，还要在端口 9400 上允许入站流量  。 这将确保 Kafka REST 代理服务器可访问。

有关网络安全组的详细信息，请参阅[网络安全组概述](../virtual-network/security-overview.md)。

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>控制 HDInsight 群集的出站流量

有关控制 HDInsight 群集的出站流量的详细信息，请参阅[配置 Azure HDInsight 群集的出站网络流量限制](hdinsight-restrict-outbound-traffic.md)。

### <a name="forced-tunneling-to-on-premises"></a>到本地的强制隧道

强制隧道是用户定义的路由配置，其中来自子网的所有流量都强制流向特定网络或位置，例如你的本地网络或防火墙。 因为数据传输量大且可能影响性能，所以在将所有数据传输回本地时，建议不要使用强制隧道__。

有兴趣设置强制隧道的客户应使用[自定义元存储](./hdinsight-use-external-metadata-stores.md)，并设置从群集子网或本地网络到这些自定义元存储的适当连接。

若要查看有关使用 Azure 防火墙设置 UDR 的示例，请参阅[配置 Azure HDInsight 群集的出站网络流量限制](hdinsight-restrict-outbound-traffic.md)。

## <a name="required-ports"></a>必需端口

如果计划使用**防火墙**并在特定端口上从外部访问群集，则需要允许你的方案所需的那些端口上的流量。 默认情况下，只要允许上一部分中所述的 Azure 管理流量到达端口443上的群集，就不需要对端口进行特殊筛选。

对于特定服务的端口列表，请参阅 [HDInsight 上的 Apache Hadoop 服务所用的端口](hdinsight-hadoop-port-settings-for-services.md)文档。

有关虚拟设备防火墙规则的详细信息，请参阅[虚拟设备方案](../virtual-network/virtual-network-scenario-udr-gw-nva.md)文档。

## <a name="next-steps"></a>后续步骤

* 有关演示如何创建 Azure 虚拟网络的代码示例和操作示例，请参阅[为 Azure HDInsight 群集创建虚拟网络](hdinsight-create-virtual-network.md)。
* 如需通过端到端示例来了解如何将 HDInsight 配置为连接到本地网络，请参阅[将 HDInsight 连接到本地网络](./connect-on-premises-network.md)。
* 有关 Azure 虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。
* 有关网络安全组的详细信息，请参阅[网络安全组](../virtual-network/security-overview.md)。
* 有关用户定义的路由的详细信息，请参阅[用户定义的路由和 IP 转发](../virtual-network/virtual-networks-udr-overview.md)。
* 有关虚拟网络的详细信息，请参阅[规划 HDInsight 的 VNET](./hdinsight-plan-virtual-network-deployment.md)。
