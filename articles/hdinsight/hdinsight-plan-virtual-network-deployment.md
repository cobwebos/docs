---
title: 规划 Azure HDInsight 的虚拟网络
description: 了解如何规划 Azure 虚拟网络部署，以将 HDInsight 连接到其他云资源或数据中心内的资源。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 1e6a21e8bf9c284c83af09885aa66b612b52ad7c
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044711"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>规划 Azure HDInsight 的虚拟网络

本文提供有关将[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)与 azure HDInsight 配合使用的背景信息。 它还讨论了在为 HDInsight 群集实现虚拟网络之前必须做出的设计和实施决策。 规划阶段完成后，可以继续为[Azure HDInsight 群集创建虚拟网络](hdinsight-create-virtual-network.md)。 有关正确配置网络安全组和用户定义的路由所需的 HDInsight 管理 IP 地址的详细信息，请参阅[HDInsight 管理 ip 地址](hdinsight-management-ip-addresses.md)。

使用 Azure 虚拟网络支持以下方案：

* 直接从本地网络连接到 HDInsight。
* 将 HDInsight 连接到 Azure 虚拟网络中的数据存储。
* 直接访问无法通过 Internet 公开访问的 [Apache Hadoop](https://hadoop.apache.org/) 服务。 例如，[Apache Kafka](https://kafka.apache.org/) API 或 [Apache HBase](https://hbase.apache.org/) Java API。

> [!IMPORTANT]
> 在 VNET 中创建 HDInsight 群集会创建多个网络资源，例如 Nic 和负载均衡器。 请勿**删除这些**网络资源，因为群集在 VNET 中正常工作需要它们。
>
> 2019年2月28日以后，将在同一 HDInsight 群集资源组中预配在 VNET 中创建的新 HDInsight 群集的网络资源（如 Nic、磅等）。 以前，这些资源是在 VNET 资源组中预配的。 不会更改当前正在运行的群集，并且不会在没有 VNET 的情况下创建这些群集。

## <a name="planning"></a>规划

计划在虚拟网络中安装 HDInsight 时，必须回答以下问题：

* 是否需要将 HDInsight 安装到现有的虚拟网络？ 或者是否正在创建新的网络？

    如果使用的是现有的虚拟网络，则可能需要修改网络配置，然后才能安装 HDInsight。 有关详细信息，请参阅[将 HDInsight 添加到现有虚拟网络](#existingvnet)一节。

* 是否要将包含 HDInsight 的虚拟网络连接到其他虚拟网络或你的本地网络？

    若要轻松地跨网络使用资源，可能需要创建自定义 DNS 并配置 DNS 转发。 有关详细信息，请参阅[连接多个网络](#multinet)一节。

* 是否想要将入站或出站流量限制/重定向到 HDInsight？

    HDInsight 与 Azure 数据中心中特定 IP 地址之间的通信必须不受限制。 此外，还必须设置几个防火墙允许端口以进行客户端通信。 有关详细信息，请参阅[控制网络流量](#networktraffic)一节。

## <a id="existingvnet"></a>将 HDInsight 添加到现有虚拟网络

使用本部分中的步骤，了解如何将 HDInsight 添加到现有 Azure 虚拟网络。

> [!NOTE]  
> 无法将现有 HDInsight 群集添加到虚拟网络中。

1. 对虚拟网络使用经典模式还是资源管理器部署模式？

    HDInsight 3.4 及更高版本要求使用资源管理器虚拟网络。 早期版本的 HDInsight 要求使用经典虚拟网络。

    如果你的现有网络是经典虚拟网络，则必须创建资源管理器虚拟网络，然后连接这两者。 [将经典 VNet 连接到新 VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)。

    加入后，资源管理器网络中安装的 HDInsight 就可以与经典网络中的资源进行交互了。

2. 是否使用网络安全组、用户定义路由或虚拟网络设备来限制流量进出虚拟网络？

    作为托管服务，HDInsight 需要无限制访问 Azure 数据中心中的若干个 IP 地址。 若要允许与这些 IP 地址进行通信，请更新任何现有网络安全组或用户定义的路由。
    
    HDInsight 托管多个服务，这些服务使用不同的端口。 请勿阻止发往这些端口的流量。 有关虚拟设备防火墙的允许端口列表，请参阅“安全”一节。
    
    若要查找你现有的安全配置，请使用以下 Azure PowerShell 或 Azure CLI 命令：

    * 网络安全组

        将 `RESOURCEGROUP` 替换为包含虚拟网络的资源组的名称，然后输入以下命令：
    
        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```
    
        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        有关详细信息，请参阅[排查网络安全组问题](../virtual-network/diagnose-network-traffic-filter-problem.md)一文。

        > [!IMPORTANT]  
        > 已根据规则优先级按顺序应用网络安全组规则。 应用与流量模式匹配的第一个规则，不会对该流量应用其他规则。 从最高权限到最低权限排序规则。 有关详细信息，请参阅[使用网络安全组筛选网络流量](../virtual-network/security-overview.md)文档。

    * 用户定义的路由

        将 `RESOURCEGROUP` 替换为包含虚拟网络的资源组的名称，然后输入以下命令：

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        有关详细信息，请参阅[排查路由问题](../virtual-network/diagnose-network-routing-problem.md)文档。

3. 创建一个 HDInsight 群集，并在配置过程中选择 Azure 虚拟网络。 使用以下文档中的步骤了解群集创建过程：

    * [Create HDInsight using the Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md)（使用 Azure 门户创建 HDInsight）
    * [Create HDInsight using Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)（使用 Azure PowerShell 创建 HDInsight）
    * [使用 Azure 经典 CLI 创建 HDInsight](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [使用 Azure 资源管理器模板创建 HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > 向虚拟网络添加 HDInsight 是一项可选的配置步骤。 请确保在配置群集时选择虚拟网络。

## <a id="multinet"></a>连接多个网络

多网络配置的最大挑战是网络之间的名称解析。

Azure 为安装在虚拟网络中的 Azure 服务提供名称解析。 此内置名称解析允许 HDInsight 使用完全限定的域名 (FQDN) 连接到以下资源：

* Internet 上的任何可用资源。 例如，microsoft.com、windowsupdate.com。

* 位于同一 Azure 虚拟网络中的任何资源（通过使用资源的内部 DNS 名称）。 例如，使用默认名称解析时，以下是分配给 HDInsight 辅助角色节点的内部 DNS 名称的示例：

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    这两个节点通过使用内部 DNS 名称可以直接彼此通信，以及与 HDInsight 中的其他节点进行通信。

默认名称解析不允许 HDInsight 解析加入到虚拟网络的网络中的资源名称。 例如，将本地网络加入到虚拟网络是很常见的。 仅通过默认名称解析，HDInsight 无法按名称访问本地网络中的资源。 反过来也是如此，本地网络中的资源也不能按名称访问虚拟网络中的资源。

> [!WARNING]  
> 必须创建自定义 DNS 服务器并配置虚拟网络以在创建 HDInsight 群集前使用它。

若要启用虚拟网络和已加入网络中的资源之间的名称解析，必须执行以下操作：

1. 在你计划安装 HDInsight 的 Azure 虚拟网络中创建自定义 DNS 服务器。

2. 配置虚拟网络以使用自定义 DNS 服务器。

3. 查找 Azure 为你的虚拟网络分配的 DNS 后缀。 该值类似于 `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`。 有关查找 DNS 后缀的信息，请参阅[示例：自定义 DNS](hdinsight-create-virtual-network.md#example-dns) 一节。

4. 配置 DNS 服务器之间的转发。 配置具体取决于远程网络的类型。

   * 如果远程网络是本地网络，请按如下所示配置 DNS：
        
     * __自定义 DNS__（虚拟网络中）：

         * 将虚拟网络 DNS 后缀的请求转发到 Azure 递归解析程序 (168.63.129.16)。 Azure 处理虚拟网络中资源的请求

         * 将其他所有请求转发到本地 DNS 服务器。 本地 DNS 处理所有其他名称解析请求，甚至是 Internet 资源（如 microsoft.com）的请求。

     * __本地 DNS__： 将虚拟网络 DNS 后缀的请求转发到自定义 DNS 服务器。 然后，自定义 DNS 服务器转发给 Azure 递归解析程序。

       此配置将包含虚拟网络 DNS 后缀的完全限定的域名请求路由至自定义 DNS 服务器。 其他所有请求（即使是公共 Internet 地址） 都由本地 DNS 服务器处理。

   * 如果远程网络是其他 Azure 虚拟网络，请按如下所示配置 DNS：

     * __自定义 DNS__（在每个虚拟网络中）：

         * 虚拟网络 DNS 后缀的请求将转发到自定义 DNS 服务器。 每个虚拟网络中的 DNS 负责解析其网络中的资源。

         * 将所有其他请求转发到 Azure 递归解析程序。 递归解析器负责解析本地和 Internet 资源。

       每个网络的 DNS 服务器根据 DNS 后缀将请求转发到另一个服务器。 使用 Azure 递归解析程序解析其他请求。

     有关每个配置的示例，请参阅[示例：自定义 DNS](hdinsight-create-virtual-network.md#example-dns) 一节。

有关详细信息，请参阅 [VM 和角色实例的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)文档。

## <a name="directly-connect-to-apache-hadoop-services"></a>直接连接到 Apache Hadoop 服务

可以通过 `https://CLUSTERNAME.azurehdinsight.net` 连接到该群集。 此地址使用公共 IP，如果已使用 NSG 来限制来自 Internet 的传入流量，则可能无法访问此地址。 此外，在 VNet 中部署群集时，可以使用专用终结点 `https://CLUSTERNAME-int.azurehdinsight.net` 访问它。 此终结点可解析为 VNet 中的专用 IP，以进行群集访问。

若要通过虚拟网络连接到 Apache Ambari 以及其他网页，请使用以下步骤：

1. 若要发现 HDInsight 群集节点的内部完全限定的域名 (FQDN)，请使用以下其中一种方法：

    将 `RESOURCEGROUP` 替换为包含虚拟网络的资源组的名称，然后输入以下命令：

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    在返回的节点列表中，查找头节点的 FQDN，并使用这些 FQDN 连接到 Ambari 和其他 Web 服务。 例如，使用 `http://<headnode-fqdn>:8080` 访问 Ambari。

    > [!IMPORTANT]  
    > 在头节点上托管的一些服务一次只能在一个节点上处于活动状态。 如果尝试在一个头节点上访问服务并且它返回 404 错误，请切换到其他头节点。

2. 若要确定服务可用的节点和端口，请参阅 [HDInsight 的 Hadoop 服务所用的端口](./hdinsight-hadoop-port-settings-for-services.md)一文。

## <a id="networktraffic"></a>控制网络流量

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>用于控制到 HDInsight 群集的入站和出站流量的技术

可以使用以下方法控制 Azure 虚拟网络中的网络流量：

* 网络安全组(NSG) 允许你筛选往返于网络的入站和出站流量。 有关详细信息，请参阅[使用网络安全组筛选网络流量](../virtual-network/security-overview.md)文档。

* **网络虚拟设备**（NVA）只能与出站流量一起使用。 Nva 复制防火墙和路由器等设备的功能。 有关详细信息，请参阅[网络设备](https://azure.microsoft.com/solutions/network-appliances)文档。

作为托管服务，HDInsight 要求对来自 VNET 的传入和传出流量进行无限制的访问权限。 当使用 Nsg 时，必须确保这些服务仍可与 HDInsight 群集通信。

![在 Azure 自定义 VNET 中创建的 HDInsight 实体示意图](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>HDInsight 与网络安全组

如果你计划使用**网络安全组**来控制网络流量，请在安装 HDInsight 之前执行以下操作：

1. 标识你计划用于 HDInsight 的 Azure 区域。

2. 确定 HDInsight 为你所在的区域所需的服务标记。 有关详细信息，请参阅[Azure HDInsight 的网络安全组（NSG）服务标记](hdinsight-service-tags.md)。

3. 为计划安装 HDInsight 的子网创建或修改网络安全组。

    * __网络安全组__： 允许 IP 地址端口 443 上的入站流量。 这将确保 HDInsight 管理服务可以从虚拟网络外部访问群集。

有关网络安全组的详细信息，请参阅[网络安全组概述](../virtual-network/security-overview.md)。

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>控制 HDInsight 群集的出站流量

若要详细了解如何控制来自 HDInsight 群集的出站流量，请参阅[为 Azure HDInsight 群集配置出站网络流量限制](hdinsight-restrict-outbound-traffic.md)。

#### <a name="forced-tunneling-to-on-premises"></a>强制隧道到本地

强制隧道是用户定义的路由配置，其中来自子网的所有流量都强制发往特定网络或位置，例如你的本地网络。 HDInsight__不__支持强制隧道传输到本地网络。 

## <a id="hdinsight-ip"></a>需要的 IP 地址

如果使用网络安全组或用户定义的路由来控制流量，请参阅[HDInsight 管理 IP 地址](hdinsight-management-ip-addresses.md)。
    
## <a id="hdinsight-ports"></a>所需的端口

如果计划使用**防火墙**并在特定端口上从外部访问群集，则需要允许你的方案所需的那些端口上的流量。 默认情况下，只要允许上一部分中介绍的 Azure 管理流量在端口 上到达群集，则不需要特地将端443 口列入允许列表。

对于特定服务的端口列表，请参阅 [HDInsight 上的 Apache Hadoop 服务所用的端口](hdinsight-hadoop-port-settings-for-services.md)文档。

有关虚拟设备的防火墙规则的详细信息，请参阅[虚拟设备方案](../virtual-network/virtual-network-scenario-udr-gw-nva.md)文档。

## <a name="load-balancing"></a>负载平衡

创建 HDInsight 群集时，还会创建负载均衡器。 此负载均衡器的类型为[基本 SKU 级别](../load-balancer/concepts-limitations.md#skus)，它具有某些约束。 其中一项限制是，如果在不同区域有两个虚拟网络，则无法连接到基本负载均衡器。 有关详细信息，请参阅[虚拟网络常见问题解答：针对全局 vnet 对等互连的约束](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)。

## <a name="transport-layer-security"></a>传输层安全性

通过公用群集终结点连接到群集 `https://<clustername>.azurehdinsight.net` 通过群集网关节点代理。 这些连接使用称为 TLS 的协议进行保护。 在网关上强制执行较高版本的 TLS 可提高这些连接的安全性。 有关为何应使用较新版本的 TLS 的详细信息，请参阅[解决 tls 1.0 问题](https://docs.microsoft.com/security/solving-tls1-problem)。

在部署时，可以使用 resource manager 模板中的*minSupportedTlsVersion*属性控制 HDInsight 群集的网关节点支持的最低 TLS 版本。 有关示例模板，请参阅[HDInsight 最小 TLS 1.2 快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls)。 此属性支持三个值： "1.0"、"1.1" 和 "1.2"，分别对应于 TLS 1.0 +、TLS 1.1 + 和 TLS 1.2 +。 默认情况下，如果不指定此属性，Azure HDInsight 群集将接受公有 HTTPS 终结点上的 TLS 1.2 连接，以及用于向后兼容的较旧版本。 最终，HDInsight 将在所有网关节点连接上强制实施 TLS 1.2 或更高版本。

## <a name="next-steps"></a>后续步骤

* 有关创建 Azure 虚拟网络的代码示例和示例，请参阅[创建 Azure HDInsight 群集的虚拟网络](hdinsight-create-virtual-network.md)。
* 有关将 HDInsight 配置为连接到本地网络的端到端示例，请参阅[将 HDInsight 连接到本地网络](./connect-on-premises-network.md)。
* 若要在 Azure 虚拟网络中配置 Apache HBase 群集，请参阅[在 Azure 虚拟网络中的 HDInsight 上创建 Apache hbase 群集](hbase/apache-hbase-provision-vnet.md)。
* 要了解如何配置 Apache HBase 异地复制，请参阅[在 Azure 虚拟网络中设置 Apache HBase 群集复制](hbase/apache-hbase-replication.md)。
* 有关 Azure 虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。
* 有关网络安全组的详细信息，请参阅[网络安全组](../virtual-network/security-overview.md)。
* 有关用户定义的路由的详细信息，请参阅[用户定义的路由和 IP 转发](../virtual-network/virtual-networks-udr-overview.md)。
