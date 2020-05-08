---
title: 规划 Azure HDInsight 的虚拟网络
description: 了解如何规划 Azure 虚拟网络部署，以将 HDInsight 连接到其他云资源或数据中心内的资源。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: e2db6d1d60026a00fa8e766fbaa1c72975fa2e99
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82786608"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>规划 Azure HDInsight 的虚拟网络

本文提供有关将 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md) (VNet) 与 Azure HDInsight 配合使用的背景信息。 其中介绍了在为 HDInsight 群集实施虚拟网络之前必须做出的设计和实施决策。 规划阶段完成后，可以继续[为 Azure HDInsight 群集创建虚拟网络](hdinsight-create-virtual-network.md)。 有关正确配置网络安全组 (NSG) 和用户定义的路由所需的 HDInsight 管理 IP 地址的详细信息，请参阅 [HDInsight 管理 IP 地址](hdinsight-management-ip-addresses.md)。

使用 Azure 虚拟网络可以实现以下方案：

* 直接从本地网络连接到 HDInsight。
* 在 Azure 虚拟网络中将 HDInsight 连接到数据存储。
* 直接访问无法通过 Internet 公开访问的 Apache Hadoop 服务。 例如，Apache Kafka API 或 Apache HBase Java API。

> [!IMPORTANT]
> 在 VNET 中创建 HDInsight 群集时会创建多个网络资源，例如 NIC 和负载均衡器。 请**勿**删除这些网络资源，因为群集需要它们才能在 VNET 中正常运行。
>
> 2019 年 2 月 28 日以后，在 VNET 中创建的新 HDInsight 群集的网络资源（例如 NIC、LB 等）会在同一 HDInsight 群集资源组中进行预配。 以前，这些资源在 VNET 资源组中预配。 当前运行的群集以及那些在没有 VNET 的情况下创建的群集没有任何更改。

## <a name="planning"></a>规划

下面是计划在虚拟网络中安装 HDInsight 时必须回答的问题：

* 是否需要将 HDInsight 安装到现有的虚拟网络？ 或者，你是否在创建新的网络？

    如果使用现有的虚拟网络，则可能需要修改网络配置，然后才能安装 HDInsight。 有关详细信息，请参阅[将 HDInsight 添加到现有的虚拟网络](#existingvnet)部分。

* 是否需要将包含 HDInsight 的虚拟网络连接到另一个虚拟网络或本地网络？

    若要轻松地跨网络使用资源，可能需要创建自定义 DNS 并配置 DNS 转发。 有关详细信息，请参阅[连接多个网络](#multinet)部分。

* 是否需要限制/重定向 HDInsight 的入站或出站流量？

    HDInsight 必须与 Azure 数据中心的特定 IP 地址进行不受限制的通信。 此外还必须通过防火墙启用数个端口，以便进行客户端通信。 有关详细信息，请参阅[控制网络流量](./control-network-traffic.md)。

## <a name="add-hdinsight-to-an-existing-virtual-network"></a><a id="existingvnet"></a>将 HDInsight 添加到现有的虚拟网络

使用本部分中的步骤，了解如何将 HDInsight 添加到现有 Azure 虚拟网络。

> [!NOTE]  
> 无法将现有 HDInsight 群集添加到虚拟网络中。

1. 是否对虚拟网络使用经典或 Resource Manager 部署模型？

    HDInsight 3.4 及更高版本需要 Resource Manager 虚拟网络。 早期版本的 HDInsight 要求使用经典虚拟网络。

    如果现有网络是经典虚拟网络，则必须创建 Resource Manager 虚拟网络，然后将这两种网络连接起来。 [将经典 VNet 连接到新的 VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)。

    连接后，安装在 Resource Manager 网络中的 HDInsight 即可与经典网络中的资源交互。

2. 是否使用网络安全组、用户定义路由或虚拟网络设备来限制进出虚拟网络的流量？

    作为托管服务，HDInsight 要求对 Azure 数据中心的多个 IP 地址进行不受限制的访问。 请更新任何现有的网络安全组或用户定义路由，以便与这些 IP 地址通信。

    HDInsight 托管多个服务，这些服务使用不同的端口。 不要阻止发往这些端口的流量。 有关虚拟设备防火墙的允许端口列表，请参阅“安全”一节。

    若要查找现有的安全配置，请使用以下 Azure PowerShell 或 Azure CLI 命令：

    * 网络安全组

        将 `RESOURCEGROUP` 替换为包含虚拟网络的资源组的名称，然后输入命令：

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        有关详细信息，请参阅[排查网络安全组问题](../virtual-network/diagnose-network-traffic-filter-problem.md)文档。

        > [!IMPORTANT]  
        > 网络安全组规则按规则优先级顺序应用。 将应用与流量模式匹配的第一条规则，而不应用该流量的其他规则。 权限级别从最高到最低排列的顺序规则。 有关详细信息，请参阅[使用网络安全组筛选网络流量](../virtual-network/security-overview.md)文档。

    * 用户定义路由

        将 `RESOURCEGROUP` 替换为包含虚拟网络的资源组的名称，然后输入命令：

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        有关详细信息，请参阅[排查路由问题](../virtual-network/diagnose-network-routing-problem.md)文档。

3. 创建一个 HDInsight 群集，并在配置过程中选择 Azure 虚拟网络。 使用以下文档中的步骤了解群集创建过程：

    * [Create HDInsight using the Azure portal（使用 Azure 门户创建 HDInsight）](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Create HDInsight using Azure PowerShell（使用 Azure PowerShell 创建 HDInsight）](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [使用 Azure 经典 CLI 创建 HDInsight](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [使用 Azure 资源管理器模板创建 HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > 向虚拟网络添加 HDInsight 是一项可选的配置步骤。 请确保在配置群集时选择虚拟网络。

## <a name="connecting-multiple-networks"></a><a id="multinet"></a>连接多个网络

多网络配置的最大难题是在网络之间进行名称解析。

Azure 为安装在虚拟网络中的 Azure 服务提供名称解析。 此内置的名称解析功能允许 HDInsight 使用完全限定的域名 (FQDN) 连接到以下资源：

* 在 Internet 上提供的任何资源。 例如，microsoft.com、windowsupdate.com。

* 同一 Azure 虚拟网络中能够使用资源的内部 DNS 名称  连接的的任何资源。 例如，在使用默认的名称解析时，下面是分配给 HDInsight 工作器节点的内部 DNS 名称示例：

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    这两个节点均可使用内部 DNS 名称直接相互通信，以及与 HDInsight 中的其他节点通信。

 默认名称解析不允许 HDInsight 解析连接到虚拟网络的网络中的资源的名称。 例如，将本地网络加入虚拟网络很常见。 如果仅使用默认名称解析，HDInsight 将无法通过名称访问本地网络中的资源。 相反，本地网络中的资源不能按名称访问虚拟网络中的资源。

> [!WARNING]  
> 必须在创建 HDInsight 群集之前，先创建自定义 DNS 服务器并将虚拟网络配置为使用该服务器。

若要在虚拟网络和已连接网络中的资源之间启用名称解析，必须执行以下操作：

1. 在计划安装 HDInsight 的 Azure 虚拟网络中创建自定义 DNS 服务器。

2. 将虚拟网络配置为使用自定义 DNS 服务器。

3. 为虚拟网络找出 Azure 分配的 DNS 后缀。 该值类似于 `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`。 有关查找 DNS 后缀的信息，请参阅[示例：自定义 DNS](hdinsight-create-virtual-network.md#example-dns) 部分。

4. 配置 DNS 服务器之间的转发。 此配置取决于远程网络的类型。

   * 如果远程网络为本地网络，请将 DNS 配置如下：

     *  自定义 DNS（位于虚拟网络中）：

         * 将针对虚拟网络 DNS 后缀的请求转发到 Azure 递归解析程序 (168.63.129.16)。 Azure 处理虚拟网络中资源的请求

         * 将所有其他请求转发到本地 DNS 服务器。 本地 DNS 处理所有其他的名称解析请求，甚至包括 Internet 资源（例如 Microsoft.com）的请求。

     * __本地 DNS__：将虚拟网络 DNS 后缀的请求转发到自定义 DNS 服务器。 然后，自定义 DNS 服务器转发给 Azure 递归解析程序。

       此配置将完全限定的域名（其中包含虚拟网络的 DNS 后缀）的请求路由到自定义 DNS 服务器。 所有其他请求（甚至包括对公共 Internet 地址的请求）由本地 DNS 服务器处理。

   * 如果远程网络为另一 Azure 虚拟网络，请将 DNS 配置如下：

     *  自定义 DNS（位于每个虚拟网络中）：

         * 对虚拟网络 DNS 后缀的请求将转发到自定义 DNS 服务器。 每个虚拟网络中的 DNS 负责解析其网络中的资源。

         * 将所有其他请求转发到 Azure 递归解析程序。 递归解析程序负责解析本地资源和 Internet 资源。

       每个网络的 DNS 服务器根据 DNS 后缀将请求转发到其他服务器。 其他请求使用 Azure 递归解析程序进行解析。

     有关每个配置的示例，请参阅[示例：自定义 DNS](hdinsight-create-virtual-network.md#example-dns) 部分。

有关详细信息，请参阅 [VM 和角色实例的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)文档。

## <a name="directly-connect-to-apache-hadoop-services"></a>直接连接到 Apache Hadoop 服务

可以通过 `https://CLUSTERNAME.azurehdinsight.net` 连接到该群集。 此地址使用公共 IP，如果已使用 NSG 来限制来自 Internet 的传入流量，则可能无法访问此地址。 此外，在 VNet 中部署群集时，可以使用专用终结点 `https://CLUSTERNAME-int.azurehdinsight.net` 访问它。 此终结点可解析为 VNet 中的专用 IP，以进行群集访问。

若要通过虚拟网络连接到 Apache Ambari 以及其他网页，请使用以下步骤：

1. 若要发现 HDInsight 群集节点的内部完全限定的域名 (FQDN)，请使用以下方法之一：

    将 `RESOURCEGROUP` 替换为包含虚拟网络的资源组的名称，然后输入命令：

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
    > 托管在头节点上的某些服务一次只能在一个节点上处于活动状态。 如果尝试在一个头节点上访问服务并且它返回 404 错误，请切换到其他头节点。

2. 若要确定服务可用的节点和端口，请参阅 [HDInsight 的 Hadoop 服务所用的端口](./hdinsight-hadoop-port-settings-for-services.md)一文。

## <a name="load-balancing"></a>负载均衡

创建 HDInsight 群集时，也会创建一个负载均衡器。 此负载均衡器的类型为[基本 SKU 级别](../load-balancer/skus.md)，它具有某些约束。 这些约束中的一个是：如果两个虚拟网络位于不同的区域，则无法连接到基本负载均衡器。 有关详细信息，请参阅[虚拟网络常见问题解答：对全局 VNet 对等互连的约束](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)。

## <a name="next-steps"></a>后续步骤

* 有关演示如何创建 Azure 虚拟网络的代码示例和操作示例，请参阅[为 Azure HDInsight 群集创建虚拟网络](hdinsight-create-virtual-network.md)。
* 有关将 HDInsight 配置为连接到本地网络的端到端示例，请参阅[将 HDInsight 连接到本地网络](./connect-on-premises-network.md)。
* 有关 Azure 虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。
* 有关网络安全组的详细信息，请参阅[网络安全组](../virtual-network/security-overview.md)。
* 有关用户定义的路由的详细信息，请参阅[用户定义的路由和 IP 转发](../virtual-network/virtual-networks-udr-overview.md)。
* 有关控制流量的详细信息，请参阅[控制网络流量](./control-network-traffic.md)。
