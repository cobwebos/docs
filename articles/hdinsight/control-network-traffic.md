---
title: 在 Azure HDInsight 中控制网络流量
description: 了解控制进出 Azure HDInsight 群集的入站和出站流量的方法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 54a55789cf867c97cf2384b48f1e5545ee54dafc
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773400"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>在 Azure HDInsight 中控制网络流量

可以使用以下方法控制 Azure 虚拟网络中的网络流量：

* 网络安全组(NSG) 允许你筛选往返于网络的入站和出站流量。 有关详细信息，请参阅[使用网络安全组筛选网络流量](../virtual-network/security-overview.md)文档。

* 网络虚拟设备 (NVA) 仅可用于出站流量。 NVA 会复制防火墙和路由器等设备功能。 有关详细信息，请参阅[网络设备](https://azure.microsoft.com/solutions/network-appliances)文档。

HDInsight 是一项托管服务，它需要对 HDinsight 运行状况和管理服务具有不受限制的访问权限，才能处理从 VNET 传入和传出的流量。 使用 NSG 时，必须确保这些服务仍可与 HDInsight 通信。

![在 Azure 自定义 VNET 中创建的 HDInsight 实体的关系图](./media/control-network-traffic/hdinsight-vnet-diagram.png)

## <a name="hdinsight-with-network-security-groups"></a>使用网络安全组的 HDInsight

如果你计划使用网络安全组来控制网络流量，请在安装 HDInsight 前执行以下操作：

1. 标识你计划用于 HDInsight 的 Azure 区域。

2. 确定在你的区域中 HDInsight 所需的服务标记。 有关详细信息，请参阅 [Azure HDInsight 的网络安全组 (NSG) 服务标记](hdinsight-service-tags.md)。

3. 针对你计划安装 HDInsight 的子网创建或修改网络安全组。

    * __网络安全组__： 允许 IP 地址端口 443 上的入站 流量。 这将确保 HDInsight 管理服务可从虚拟网络外部访问群集。 对于启用了 Kafka REST 代理的群集，还要在端口 9400 上允许入站流量  。 这将确保 Kafka REST 代理服务器可访问。

有关网络安全组的详细信息，请参阅[网络安全组概述](../virtual-network/security-overview.md)。

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>控制 HDInsight 群集的出站流量

要详细了解如何控制 HDInsight 群集的出站流量，请参阅[配置 Azure HDInsight 群集的出站网络流量限制](hdinsight-restrict-outbound-traffic.md)。

### <a name="forced-tunneling-to-on-premises"></a>到本地的强制隧道

强制隧道是用户定义的路由配置，其中来自子网的所有流量都强制流向特定网络或位置，例如你的本地网络或防火墙。 由于数据传输量大且可能影响性能，因此在将所有数据传输回本地时，建议不要使用强制隧道。

有兴趣设置强制隧道的客户应使用[自定义元存储](./hdinsight-use-external-metadata-stores.md)，并设置从群集子网或本地网络通往这些自定义元存储的适当连接。

若要查看有关使用 Azure 防火墙设置 UDR 的示例，请参阅[配置 Azure HDInsight 群集的出站网络流量限制](hdinsight-restrict-outbound-traffic.md)。

## <a name="required-ip-addresses"></a>需要的 IP 地址

如果使用网络安全组或用户定义的路由来控制流量，请参阅 [HDInsight 管理 IP 地址](hdinsight-management-ip-addresses.md)。

## <a name="required-ports"></a>必需端口

如果计划使用**防火墙**并在特定端口上从外部访问群集，则需要允许你的方案所需的那些端口上的流量。 默认情况下，只要允许上一部分中介绍的 Azure 管理流量在端口 443 上到达群集，就不需要特地将端口列入允许列表。

对于特定服务的端口列表，请参阅 [HDInsight 上的 Apache Hadoop 服务所用的端口](hdinsight-hadoop-port-settings-for-services.md)文档。

有关虚拟设备的防火墙规则的详细信息，请参阅[虚拟设备方案](../virtual-network/virtual-network-scenario-udr-gw-nva.md)文档。

## <a name="next-steps"></a>后续步骤

* 有关创建 Azure 虚拟网络的代码示例和实操示例，请参阅[为 Azure HDInsight 群集创建虚拟网络](hdinsight-create-virtual-network.md)。
* 有关将 HDInsight 配置为连接到本地网络的端到端示例，请参阅[将 HDInsight 连接到本地网络](./connect-on-premises-network.md)。
* 有关 Azure 虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。
* 有关网络安全组的详细信息，请参阅[网络安全组](../virtual-network/security-overview.md)。
* 有关用户定义的路由的详细信息，请参阅[用户定义的路由和 IP 转发](../virtual-network/virtual-networks-udr-overview.md)。
* 有关虚拟网络的详细信息，请参阅[规划 HDInsight 的 VNET](./hdinsight-plan-virtual-network-deployment.md).
