---
title: "使用虚拟网络扩展 HDInsight | Microsoft Docs"
description: "了解如何使用 Azure 虚拟网络将 HDInsight 连接到其他云资源或者你数据中心内的资源"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/20/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 52038c02901d49f787226ce629a1ca5942414e4a
ms.lasthandoff: 04/21/2017


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>使用 Azure 虚拟网络扩展 HDInsight 功能

了解如何使用具有 HDInsight 的 Azure 虚拟网络来启用以下方案：

* 限制访问 HDInsight。 例如，阻止来自 Internet 的入站流量。

* 直接访问在 Internet 上不会公开的 HDInsight 上的服务。 例如，直接使用 Kafka 中转站或使用 HBase Java API。

* 直接将服务连接到 HDInsight。 例如，使用 Oozie 将数据导入或导出到数据中心内的 SQL Server。

* 创建涉及多个 HDInsight 群集的解决方案。 例如，使用 Spark 或 Storm 分析存储在 Kafka 中的数据。

## <a name="prerequisites"></a>先决条件

* Azure CLI 2.0：有关详细信息，请参阅[安装和配置 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2)。

* Azure PowerShell：有关详细信息，请参阅[安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。

> [!NOTE]
> 本文档中的步骤需要最新版本的 Azure CLI 和 Azure PowerShell。 如果使用的是较旧版本，则命令可能会有所不同。 为获得最佳结果，请使用以上链接来安装最新版本。

## <a id="whatis"></a>什么是 Azure 虚拟网络？

[Azure 虚拟网络](https://azure.microsoft.com/documentation/services/virtual-network/)允许创建包含需要用于解决方案的资源的安全永久性网络。

以下是在虚拟网络中使用 HDInsight 时的注意事项列表：

* __经典虚拟网络和资源管理器虚拟网络__：使用下表来确定基于 HDInsight 群集操作系统要使用的网络类型：

    | HDInsight 操作系统 | 经典虚拟网络 | 资源管理器虚拟网络 |
    | ---- | ---- | ---- |
    | Linux | 否 | 是 |
    | Windows | 是 | 否 |

    若要在不兼容的虚拟网络中访问资源，可以加入两个网络。 若要深入了解如何连接经典虚拟网络和 Resource Manager 虚拟网络，请参阅[将经典 VNet 连接到新的 VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)。

* __自定义 DNS__：Azure 为安装在 Azure 虚拟网络中的 Azure 服务提供名称解析。 此名称解析不会扩展到虚拟网络之外。 若要对虚拟网络之外的资源启用名称解析，则必须使用自定义的 DNS 服务器。 有关使用自定义 DNS 服务器的详细信息，请参阅[VM 和角色实例的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)文档。

* __强制隧道__：HDInsight 不支持 Azure 虚拟网络的强制隧道配置。

* __限制网络流量__：虽然 HDInsight 的确支持使用网络安全组来限制网络流量，但需要对多个 Azure Ip 不受限制访问。 有关详细信息，请参阅[安全虚拟网络](#secured-virtual-networks)部分。

### <a name="connect-cloud-resources-together-in-a-private-network-cloud-only"></a>在专用网络（仅限云）中将云资源连接到一起

![仅限云配置示意图](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

使用虚拟网络链接 Azure 服务与 HDInsight 可实现以下方案：

* 从 Azure 虚拟机中运行的 Azure 网站或服务**调用 HDInsight 服务或作业**。

* 在 HDInsight 与 SQL 数据库或 SQL Server 或其他运行于虚拟机的数据存储解决方案之间**直接传输数据**。

* **组合多个 HDInsight 服务器**以构成单个解决方案。 HDInsight 群集具有几种不同的类型，这些类型与针对其优化该群集的工作负荷或技术相对应。 不支持在一个群集上创建合并了多个类型（如 Storm 和 HBase）的群集。 使用虚拟网络能够使多个群集在彼此之间直接进行通信。

### <a name="connect-cloud-resources-to-a-local-datacenter-network"></a>将云资源连接到本地数据中心网络

通过站点到站点配置，可将数据中心中的多个资源连接到 Azure 虚拟网络。 可以使用硬件 VPN 设备或路由与远程访问服务进行连接。

![站点到站点配置示意图](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

利用点到站点配置，你可以使用软件 VPN 将特定资源连接到 Azure 虚拟网络。

![点到站点配置示意图](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

使用虚拟网络链接云和数据中心可让类似方案在仅限云的配置上实现。 但是，如果不想受限于使用云中的资源，你也可以使用数据中心内的资源。

* 在 HDInsight 与数据中心之间**直接传输数据**。 例如，使用 Sqoop 在 SQL Server 往返传输数据，或读取业务线 (LOB) 应用程序生成的数据。

* 从 LOB 应用程序**调用 HDInsight 服务或作业**。 例如，使用 HBase Java API 来存储和检索 HDInsight HBase 群集的数据。

有关虚拟网络特性、优势和功能的详细信息，请参阅 [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。

> [!NOTE]
> 预配 HDInsight 群集前先创建 Azure 虚拟网络，然后在创建群集时指定网络。 有关详细信息，请参阅[虚拟网络配置任务](https://azure.microsoft.com/documentation/services/virtual-network/)。

## <a name="secured-virtual-networks"></a>受保护的虚拟网络

HDInsight 服务是一种托管服务，并需要在预配期间和运行时访问 Azure 管理服务。 Azure 管理执行以下服务：

* 监视群集的运行状况
* 启动群集资源的故障转移
* 通过缩放操作更改群集中的节点数
* 其他管理任务

> [!NOTE]
> 这些操作不需要完全访问 Internet。 限制 Internet 访问时，允许在端口 443 上进行以下 IP 地址的入站访问。 这将使 Azure 能够管理 HDInsight：

应允许的 IP 地址专门用于 HDInsight 群集和虚拟网络所在的区域。 使用下表查找正在使用的区域的 IP 地址。

| 国家/地区 | 区域 | 允许的 IP 地址 | 允许的端口 |
| ---- | ---- | ---- | ---- |
| 巴西 | 巴西南部 | 191.235.84.104</br>191.235.87.113 | 443 |
| 加拿大 | 加拿大东部 | 52.229.127.96</br>52.229.123.172 | 443 |
| &nbsp; | 加拿大中部 | 52.228.37.66</br>52.228.45.222 | 443 |
| 德国 | 德国中部 | 51.4.146.68</br>51.4.146.80 | 443 |
| &nbsp; | 德国东北部 | 51.5.150.132</br>51.5.144.101 | 443 |
| 印度 | 印度中部 | 52.172.153.209</br>52.172.152.49 | 443 |
| 英国 | 英国西部 | 51.141.13.110</br>51.141.7.20 | 443 |
| &nbsp; | 英国南部 | 51.140.47.39</br>51.140.52.16 | 443 |
| 美国 | 美国中西部 | 52.161.23.15</br>52.161.10.167 | 443 |
| &nbsp; | 美国西部 2 | 52.175.211.210</br>52.175.222.222 | 443 |

__如果所在区域未列在表中__，允许流量到达以下 IP 地址的端口 __443__：

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> HDInsight 不支持限制出站流量，仅可限制入站流量。 当为包含 HDInsight 的子网定义网络安全组规则时，__只能使用入站规则__。

### <a name="working-with-hdinsight-in-secured-virtual-networks"></a>在受保护的虚拟网络中使用 HDInsight

如果阻止 Internet 访问，将无法使用通常通过群集的公共网关公开的 HDInsight 服务。 这些服务包括 Ambari 和 SSH。 相反，必须使用群集头节点的内部 IP 地址来访问服务。

若要查找头节点的内部 IP 地址，请使用[内部 IP 和 FQDN](#internal-ips-and-fqdns) 部分中的脚本。

### <a name="example-secured-virtual-network"></a>示例：受保护的虚拟网络

以下示例演示如何一个创建网络安全组，该组允许以下 IP 地址的端口 443 上的入站流量：

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> 这些地址用于没有列出特定 IP 地址的区域。 若要查找你所在区域的 IP 地址，请使用[受保护的虚拟网络](#secured-virtual-networks)部分中的信息。

这些步骤假定你已创建想要将 HDInsight 安装到其中的虚拟网络和子网。 请参阅[使用 Azure 门户创建虚拟网络](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)。

> [!WARNING]
> 根据__优先级__按照网络流量测试规则。 在某个规则匹配测试条件后将被应用，并且不会再针对该请求测试更多的规则。 如果存在某个广泛阻止入站流量的规则（如**拒绝所有**规则），则它__必须__位于允许流量的规则之后。
>
> 有关网络安全组规则的详细信息，请参阅[什么是网络安全组](../virtual-network/virtual-networks-nsg.md)文档。

**示例：Azure 资源管理模板**

使用 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/)中的以下资源管理模板在 VNet 中创建具备安全网络配置的 HDInsight 群集：

[在 VNet 中部署安全的 Azure VNet 和 HDInsight Hadoop 群集](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

**示例：Azure PowerShell**

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that HDInsight will be installed into"
# Get the Virtual Network object
$vnet = Get-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName
# Get the region the Virtual network is in.
$location = $vnet.Location
# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzureRmNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound
# Set the changes to the security group
Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
# Apply the NSG to the subnet
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

**示例：Azure CLI**

1. 使用以下命令创建名为 `hdisecure` 的新网络安全组。 将 **RESOURCEGROUPNAME** 替换为包含 Azure 虚拟网络的资源组。 将 **LOCATION** 替换为组创建的位置（区域）。

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    在创建组后，将收到有关新组的信息。

2. 使用以下命令将规则添加新的网络安全组，这些规则允许从 Azure HDInsight 运行状况和管理服务通过端口 443 发起的入站通信。 将 **RESOURCEGROUPNAME** 替换为包含 Azure 虚拟网络的资源组的名称。

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    ```

3. 创建规则后，使用以下命令检索此网络安全组的唯一标识符：

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    此命令会返回类似于以下文本的值：

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    如果没有获得预期结果，请在命令中的 ID 两侧使用双引号。

4. 使用以下命令将网络安全组应用于子网。 将 __GUID__ 和 __RESOURCEGROUPNAME__ 值替换为从上一步骤中返回的值。 将 __VNETNAME__ 和 __SUBNETNAME__ 替换为在创建 HDInsight 群集时要使用的虚拟网络名称和子网名称。

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    此命令完成之后，你可以将 HDInsight 成功安装到这些步骤中使用的子网上的受保护虚拟网络。

> [!IMPORTANT]
> 使用之前的步骤只会实现对 Azure 云中 HDInsight 运行状况和管理服务的访问。 任何从虚拟网络外部对 HDInsight 群集的其他访问将会被阻止。 若要从虚拟网络之外启用访问，必须添加其他的虚拟网络安全组规则。
>
> 以下示例演示了如何从 Internet 启用 SSH 访问：
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound
> ```
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
> ```

有关网络安全组的详细信息，请参阅[网络安全组概述](../virtual-network/virtual-networks-nsg.md)。 有关在 Azure 虚拟网络中控制路由的详细信息，请参阅[用户定义的路由和 IP 转发](../virtual-network/virtual-networks-udr-overview.md)。

## <a name="retrieve-internal-ips-and-fqdns"></a>检索内部 IP 和 FQDN

使用虚拟网络连接到 HDInsight 后，将可以直接连接到群集中的节点。 使用下面的脚本来确定群集中节点的内部 IP 地址和完全限定域名 (FQDN)：

**Azure PowerShell**

```powershell
$resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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

__Azure CLI__

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

> [!IMPORTANT]
> 在 Azure CLI 2.0 示例中，将 `<resourcegroupname>` 替换为包含虚拟网络的资源组名称。

脚本通过查询群集的虚拟网络接口卡 (NIC) 运行。 NIC 存在于包含 HDInsight 使用的虚拟网络的资源组中。

## <a id="nextsteps"></a>后续步骤

以下示例演示了如何对 Azure 虚拟网络使用 HDInsight：

* [Azure 虚拟网络中的 HBase 群集](hdinsight-hbase-provision-vnet.md)

* [在 HDInsight 中使用 Storm 和 HBase 分析传感器数据](hdinsight-storm-sensor-data-analysis.md)

* [在 HDInsight 中配置 Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)

* [将 Sqoop 与 HDInsight 中的 Hadoop 配合使用](hdinsight-use-sqoop-mac-linux.md)

若要了解有关 Azure 虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。


