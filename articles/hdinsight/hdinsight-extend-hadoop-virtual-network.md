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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/01/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0587dfcd6079fc8df91bad5a5f902391d3657a6b
ms.openlocfilehash: 7038c19a4419aa3569a931e393f3d94896740209


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>使用 Azure 虚拟网络扩展 HDInsight 功能
通过 Azure 虚拟网络，可扩展 Hadoop 解决方案以合并本地资源（例如 SQL Server）、合并多个 HDInsight 群集类型或者在云中的资源之间创建安全的专用网络。

## <a name="prerequisites"></a>先决条件

* Azure CLI 2.0（预览版）：有关详细信息，请参阅[安装和配置 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2)。

* Azure PowerShell：有关详细信息，请参阅[安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

> [!NOTE]
> 本文档中的步骤需要最新版本的 Azure CLI 和 Azure PowerShell。 如果使用的是较旧版本，则命令可能会有所不同。 为获得最佳结果，请使用以上链接来安装最新版本。

## <a name="a-idwhatisawhat-is-azure-virtual-network"></a><a id="whatis"></a>Azure 虚拟网络是什么？

[Azure 虚拟网络](https://azure.microsoft.com/documentation/services/virtual-network/)允许创建包含需要用于解决方案的资源的安全永久性网络。 通过虚拟网络，你可以：

* 在专用网络（仅限云）中将云资源连接在一起。
  
    ![仅限云配置示意图](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)
  
    使用虚拟网络链接 Azure 服务与 HDInsight 可实现以下方案：
  
    * 从 Azure 虚拟机中运行的 Azure 网站或服务**调用 HDInsight 服务或作业**。
    * 在 HDInsight 与 SQL 数据库或 SQL Server 或其他运行于虚拟机的数据存储解决方案之间**直接传输数据**。
    * **组合多个 HDInsight 服务器**以构成单个解决方案。 HDInsight 群集具有各种不同的类型，与该群集进行优化的工作负荷或技术相对应。 不支持在一个群集上创建合并了多个类型（如 Storm 和 HBase）的群集。 使用虚拟网络能够使多个群集在彼此之间直接进行通信。

* 使用虚拟专用网络 (VPN) 将云资源连接到本地数据中心网络（站点到站点，或点到站点）。
  
    利用站点到站点配置，你可以使用硬件 VPN 或路由和远程访问服务将多个资源从数据中心连接到 Azure 虚拟网络。
  
    ![站点到站点配置示意图](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)
  
    利用点到站点配置，你可以使用软件 VPN 将特定资源连接到 Azure 虚拟网络。
  
    ![点到站点配置示意图](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)
  
    使用虚拟网络链接云和数据中心可让类似方案在仅限云的配置上实现。 但是，如果不想受限于使用云中的资源，你也可以使用数据中心内的资源。
  
    * 在 HDInsight 与数据中心之间**直接传输数据**。 例如，使用 Sqoop 在 SQL Server 往返传输数据，或读取业务线 (LOB) 应用程序生成的数据。
    * 从 LOB 应用程序**调用 HDInsight 服务或作业**。 例如，使用 HBase Java API 来存储和检索 HDInsight HBase 群集的数据。

有关虚拟网络特性、优势和功能的详细信息，请参阅 [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。

> [!NOTE]
> 你必须创建 Azure 虚拟网络才能设置一个 HDInsight 群集。 有关详细信息，请参阅[虚拟网络配置任务](https://azure.microsoft.com/documentation/services/virtual-network/)。

## <a name="virtual-network-requirements"></a>虚拟网络要求

> [!IMPORTANT]
> 在虚拟网络上创建 HDInsight 群集需要本部分中所述的特定虚拟网络配置。

### <a name="location-based-virtual-networks"></a>基于位置的虚拟网络

Azure HDInsight 仅支持基于位置的虚拟网络，目前无法处理基于地缘的虚拟网络。

### <a name="classic-or-v2-virtual-network"></a>经典或 V2 虚拟网络

基于 Windows 的群集需要经典虚拟网络，而基于 Linux 的群集需要 Azure Resource Manager 虚拟网络。 如果没有正确的网络类型，创建群集时它将不能使用。

如果具有计划创建的群集不可用的虚拟网络上的资源，可以新建一个该群集可用的虚拟网络，并将其连接到不兼容的虚拟网络。 然后可以在其要求的网络版本中创建群集，由于两者已联接，该群集将能够访问另一个网络中的资源。 若要深入了解如何连接经典虚拟网络和新虚拟网络，请参阅[将经典 VNet 连接到新的 VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)。

### <a name="custom-dns"></a>自定义 DNS

创建虚拟网络时，Azure 将为网络中安装的 Azure 服务（例如 HDInsight）提供默认名称解析。 但是，对于跨网络域名解析等情况，你可能需要使用自己的域名系统 (DNS)。 例如，在位于两个联接的虚拟网络中的服务之间通信时。 与 Azure 虚拟网络配合使用时，HDInsight 同时支持默认 Azure 名称解析和自定义 DNS。

若要深入了解如何配合 Azure 虚拟网络使用自己的 DNS 服务器，请参阅 [VM 和角色实例的名称解析文档](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)中的“使用自己的 DNS 服务器的名称解析”部分。

### <a name="secured-virtual-networks"></a>受保护的虚拟网络

HDInsight 服务是一个托管的服务，需要在预配期间和运行时访问 Internet。 这样，Azure 便可以监视群集的运行状况、启动群集资源故障转移、通过缩放操作更改群集中的节点数，以及执行其他管理任务。

如果需要将 HDInsight 安装到受保护的虚拟网络，必须允许以下 IP 地址通过端口 443 进行入站访问，使 Azure 能够管理 HDInsight 群集。

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

如果允许这些地址通过端口 443 进行入站访问，则你可以成功地将 HDInsight 安装到受保护的虚拟网络。

> [!IMPORTANT]
> HDInsight 不支持限制出站流量，仅可限制入站流量。 在定义包含 HDInsight 的子网的网络安全组规则时，仅使用入站规则。

以下示例演示如何新建一个允许所需地址的网络安全组，并将该安全组应用于虚拟网络中的子网。 这些步骤假定你已创建想要将 HDInsight 安装到其中的虚拟网络和子网。

> [!IMPORTANT]
> 请注意这些示例中使用的 `priority` 值；根据优先级顺序针对网络流量对规则进行测试。 当某个规则符合测试条件并应用后，就不会再测试其他的规则。
> 
> 如果有广泛阻止入站流量的自定义规则（例如**全部拒绝**规则），则可能需要调整这些示例中或自定义规则中的优先级值，使示例中的规则在阻止访问的规则之前出现。 否则，将先测试**全部拒绝**规则，而不会再应用此示例中的规则。 还必须注意不要阻止 Azure 虚拟网络的默认规则。 例如，不应创建应用于默认的**允许 VNET 入站**规则（其优先级为 65000）前的**全部拒绝**规则。
> 
> 若要深入了解如何应用规则以及默认入站和出站规则，请参阅 [What is a Network Security Group?](../virtual-network/virtual-networks-nsg.md)（什么是网络安全组？）。


**使用 Azure PowerShell**

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
    # Create a new Network Security Group.
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

**使用 Azure CLI**

1. 使用以下命令创建名为 `hdisecure` 的新网络安全组。 将 **RESOURCEGROUPNAME** 和 **LOCATION** 分别替换为包含 Azure 虚拟网络的资源组以及在其中创建组的位置（区域）。
   
        az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION

    创建组后，你将收到有关新组的信息。

2. 使用以下命令将规则添加新的网络安全组，这些规则允许从 Azure HDInsight 运行状况和管理服务通过端口 443 发起的入站通信。 将 **RESOURCEGROUPNAME** 替换为包含 Azure 虚拟网络的资源组的名称。
    
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"

3. 创建规则后，使用以下命令检索此网络安全组的唯一标识符：

        az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'

    这会返回类似于以下文本的值：

        "/subscriptions/55b1016c-0f27-43d2-b908-b8c373d6d52e/resourceGroups/mygroup/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

4. 使用以下命令将网络安全组应用于子网。 将 __GUID__ 和 __RESOURCEGROUPNAME__ 值替换为从上一步骤中返回的值。 将 __VNETNAME__ 和 __SUBNETNAME__ 替换为在创建 HDInsight 群集时要使用的虚拟网络名称和子网名称。
   
        az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
   
    此命令完成之后，你可以将 HDInsight 成功安装到这些步骤中使用的子网上的受保护虚拟网络。

> [!IMPORTANT]
> 使用上述步骤只会实现对 Azure 云中 HDInsight 运行状况和管理服务的访问。 这一操作让你能够成功地将 HDInsight 群集安装到子网，但默认阻止从虚拟网络外部对 HDInsight 群集进行访问。 如果想要启用从虚拟网络外部进行访问，将需要添加其他网络安全组规则。
> 
> 例如，若要允许来自 Internet 的 SSH 访问，需要添加类似于下面的规则： 
> 
> * Azure PowerShell - ```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * Azure CLI - ```az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"```

有关网络安全组的详细信息，请参阅[网络安全组概述](../virtual-network/virtual-networks-nsg.md)。 有关在 Azure 虚拟网络中控制路由的详细信息，请参阅[用户定义的路由和 IP 转发](../virtual-network/virtual-networks-udr-overview.md)。

## <a name="a-idtasksatasks-and-information"></a><a id="tasks"></a>任务和信息

本部分包含常见任务信息和搭配使用 HDInsight 与虚拟网络时可能需要的信息。

### <a name="determine-the-fqdn"></a>确定 FQDN

系统将为 HDInsight 群集分配虚拟网络接口的特定完全限定域名 (FQDN)。 在从虚拟网络上的其他资源连接到群集时，应该使用这个地址。 若要确定 FQDN，请使用以下 URL 来查询 Ambari 管理服务:

    https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [!NOTE]
> 有关如何将 Ambari 与 HDInsight 配合使用的详细信息，请参阅[使用 Ambari API 监视 HDInsight 中的 Hadoop 群集](hdinsight-monitor-use-ambari-api.md)。

必须指定群集名称和群集上运行的服务和组件，例如 YARN 资源管理器。

> [!NOTE]
> 返回的数据是一个 JavaScript 对象表示法 (JSON) 文档，其中包含有关组件的大量信息。 若只要提取 FQDN，你应该使用 JSON 分析器来检索 `host_components[0].HostRoles.host_name` 值。

例如，若要从 HDInsight Hadoop 群集返回 FQDN，可以使用以下其中一种方法检索 YARN 资源管理器的数据：

* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
  
        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix
  
        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
  
        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/        components/resourcemanager"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        $FQDN = $JsonObject.host_components[0].HostRoles.host_name
        Write-host $FQDN

* [cURL](http://curl.haxx.se/) 和 [jq](http://stedolan.github.io/jq/)
  
        curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

### <a name="connecting-to-hbase"></a>连接到 HBase

若要使用 Java API 远程连接到 HBase，必须确定 HBase 群集的 Zookeeper 仲裁地址，并在应用程序中加以指定。

若要获取 Zookeeper 仲裁地址，请使用以下其中一种方法来查询 Ambari 管理服务：

* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
  
        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix
  
        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
  
        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [cURL](http://curl.haxx.se/) 和 [jq](http://stedolan.github.io/jq/)
  
        curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [!NOTE]
> 有关如何将 Ambari 与 HDInsight 配合使用的详细信息，请参阅[使用 Ambari API 监视 HDInsight 中的 Hadoop 群集](hdinsight-monitor-use-ambari-api.md)。

获取仲裁信息后，请将其用于客户端应用程序中。

例如，对于使用 HBase API 的 Java 应用程序，可以将 **hbase-site.xml** 文件添加到项目，并在此文件中指定仲裁信息，如下所示：

```xml
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

### <a name="verify-network-connectivity"></a>验证网络连接

某些服务，例如 SQL Server，可能会限制传入的网络连接。 这会使得 HDInsight 无法成功使用这些服务。

如果你在从 HDInsight 访问服务时遇到问题，请参阅相关服务文档，以确保启用网络访问功能。 你也可以通过在相同虚拟网络上创建 Azure 虚拟机来验证网络访问功能，并使用客户端实用工具来验证虚拟机是否可以通过虚拟网络连接服务。

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>后续步骤

以下示例演示了如何对 Azure 虚拟网络使用 HDInsight：

* [使用 HDInsight 中的 Storm 和 HBase 分析传感器数据](hdinsight-storm-sensor-data-analysis.md) - 演示如何在虚拟网络中配置 Storm 和 HBase 群集，以及如何从 Storm 将数据远程写入到 HBase。
* [在 HDInsight 中设置 Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md) - 提供有关预配 Hadoop 群集的信息，包括有关使用 Azure 虚拟网络的信息。
* [将 Sqoop 与 HDInsight 中的 Hadoop 配合使用](hdinsight-use-sqoop-mac-linux.md) - 提供有关使用 Sqoop 通过虚拟网络传输 SQL Server 数据的信息。

若要了解有关 Azure 虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。




<!--HONumber=Dec16_HO2-->


