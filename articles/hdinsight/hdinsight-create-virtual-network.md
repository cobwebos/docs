---
title: 为 Azure HDInsight 群集创建虚拟网络
description: 了解如何创建 Azure 虚拟网络，以将 HDInsight 连接到其他云资源或数据中心内的资源。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 6fd23e3d41dda15b1ec439c1e8b02073722b8871
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71073634"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>为 Azure HDInsight 群集创建虚拟网络

本文通过操作示例与代码示例来演示如何创建和配置用于 Azure HDInsight 群集的 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)。 其中提供了有关创建网络安全组 (NSG) 和配置 DNS 的详细示例。 

有关在 Azure HDInsight 中使用虚拟网络的背景信息，请参阅[规划 Azure HDInsight 的虚拟网络](hdinsight-plan-virtual-network-deployment.md)。

## <a name="prerequisites-for-code-samples-and-examples"></a>代码示例和示例的先决条件

在执行本文中的任何代码示例之前，应了解 TCP/IP 网络。 如果你不熟悉 TCP/IP 网络，请在修改生产网络之前咨询相关的人员。

本文中的示例要求满足的其他先决条件包括：

* 如果使用 PowerShell，则需要安装 [AZ 模块](https://docs.microsoft.com/powershell/azure/overview)。
* 若要使用 Azure CLI 但尚未安装它，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

> [!IMPORTANT]  
> 如果正在查找有关如何使用 Azure 虚拟网络将 HDInsight 连接到本地网络的分步指南，请参阅[将 HDInsight 连接到本地网络](connect-on-premises-network.md)文档。

## <a id="hdinsight-nsg"></a>示例：网络安全组与 HDInsight

本节中的示例演示如何创建可允许 HDInsight 与 Azure 管理服务进行通信的网络安全组规则。 使用示例之前，修改 IP 地址以便与正在使用的 Azure 区域的 IP 地址匹配。 在 [HDInsight 管理 IP 地址](hdinsight-management-ip-addresses.md)中可找到此信息。

### <a name="azure-resource-management-template"></a>Azure 资源管理模板

以下资源管理模板创建可限制入站流量的虚拟网络，但允许来自 HDInsight 所需的 IP 地址的流量。 此模板还在虚拟网络中创建 HDInsight 群集。

* [部署安全的 Azure 虚拟网络和 HDInsight Hadoop 群集](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

使用以下 PowerShell 脚本创建可限制入站流量的虚拟网络，但允许来自北欧区域的 IP 地址的流量。

> [!IMPORTANT]  
> 更改此示例中的 `hdirule1` 和 `hdirule2` 的 IP 地址，使之与要使用的 Azure 区域匹配。 在 [HDInsight 管理 IP 地址](hdinsight-management-ip-addresses.md)中可找到此信息。

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"

# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName

# Get the region the Virtual network is in.
$location = $vnet.Location

# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName

# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `

# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg

# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

本示例演示如何添加规则以允许所需 IP 地址的入站流量。 它不包含用于限制其他源的入站访问的规则。 以下代码演示如何允许来自 Internet 的 SSH 访问：

```powershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Azure CLI

使用以下步骤创建可限制入站流量的虚拟网络，但允许来自 HDInsight 所需的 IP 地址的流量。

1. 使用以下命令创建名为 `hdisecure` 的新网络安全组。 将 `RESOURCEGROUP` 替换为包含 Azure 虚拟网络的资源组。 将 `LOCATION` 替换为组创建在的位置（区域）。

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    在创建组后，将收到有关新组的信息。

2. 使用以下命令将规则添加新的网络安全组，这些规则允许从 Azure HDInsight 运行状况和管理服务通过端口 443 发起的入站通信。 将 `RESOURCEGROUP` 替换为包含 Azure 虚拟网络的资源组的名称。

    > [!IMPORTANT]  
    > 更改此示例中的 `hdirule1` 和 `hdirule2` 的 IP 地址，使之与要使用的 Azure 区域匹配。 在 [HDInsight 管理 IP 地址](hdinsight-management-ip-addresses.md)中可找到此信息。

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. 要检索此网络安全组的唯一标识符，请使用以下命令：

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    此命令会返回类似于以下文本的值：

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

4. 使用以下命令将网络安全组应用于子网。 将 `GUID` 和 `RESOURCEGROUP` 值替换为从上一步骤中返回的值。 将 `VNETNAME` 和 `SUBNETNAME` 替换为要创建的虚拟网络名称和子网名称。

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    此命令完成后，就可以将 HDInsight 安装到虚拟网络中了。


这些步骤只会实现对 Azure 云中 HDInsight 运行状况和管理服务的访问。 任何从虚拟网络外部对 HDInsight 群集的其他访问会被阻止。 若要从虚拟网络之外启用访问，必须添加其他的虚拟网络安全组规则。

以下代码演示如何允许来自 Internet 的 SSH 访问：

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a id="example-dns"></a> 示例：DNS 配置

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>虚拟网络与连接到本地网络之间的 DNS 名称解析

此示例作出以下假设：

* 你的 Azure 虚拟网络已使用 VPN 网关连接到本地网络。

* 虚拟网络中的自定义 DNS 服务器运行的是 Linux 或 Unix 操作系统。

* 自定义 DNS 服务器上安装了 [Bind](https://www.isc.org/downloads/bind/)。

在虚拟网络中的自定义 DNS 服务器上：

1. 使用 Azure PowerShell 或 Azure CLI 来查找虚拟网络的 DNS 后缀：

    将 `RESOURCEGROUP` 替换为包含虚拟网络的资源组的名称，然后输入命令：

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. 在虚拟网络的自定义 DNS 服务器上，使用以下文本作为 `/etc/bind/named.conf.local` 文件的内容：

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    将 `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` 值替换为虚拟网络的 DNS 后缀。

    此配置将虚拟网络 DNS 后缀的所有 DNS 请求路由到 Azure 递归解析程序。

2. 在虚拟网络的自定义 DNS 服务器上，使用以下文本作为 `/etc/bind/named.conf.options` 文件的内容：

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * 将 `10.0.0.0/16` 值替换为虚拟网络的 IP 地址范围。 此条目允许属于此范围内的名称解析请求地址。

    * 将本地网络的 IP 地址范围添加到 `acl goodclients { ... }` 部分。  条目允许来自本地网络中资源的名称解析请求。
    
    * 将值 `192.168.0.1` 替换为本地 DNS 服务器的 IP 地址。 此条目可将所有其他 DNS 请求路由到本地 DNS 服务器。

3. 若要使用配置，请重新启动 Bind。 例如， `sudo service bind9 restart` 。

4. 将一个条件转发器添加到本地 DNS 服务器。 配置条件转发器，以便将步骤 1 中 DNS 后缀的请求发送到自定义 DNS 服务器。

    > [!NOTE]  
    > 请查阅 DNS 软件的文档，具体了解如何添加条件转发器。

完成这些步骤后，就可以使用完全限定的域名 (FQDN) 连接到任一网络中的资源了。 现在可以将 HDInsight 安装到虚拟网络。

### <a name="name-resolution-between-two-connected-virtual-networks"></a>两个相连虚拟网络之间的名称解析

本示例做出如下假设：

* 你有两个 Azure 虚拟网络，它们使用 VPN 网关相连或对等互连。

* 两个网络中的自定义 DNS 服务器运行的是 Linux 或 Unix 操作系统。

* 自定义 DNS 服务器上安装了 [Bind](https://www.isc.org/downloads/bind/)。

1. 使用 Azure PowerShell 或 Azure CLI 来查找两个虚拟网络的 DNS 后缀：

    将 `RESOURCEGROUP` 替换为包含虚拟网络的资源组的名称，然后输入命令：

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. 使用以下文本作为自定义 DNS 服务器上 `/etc/bind/named.config.local` 文件的内容。 在这两个虚拟网络中的自定义 DNS 服务器上进行此更改。

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    将 `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` 值替换为另一个虚拟网络的 DNS 后缀。 此条目将远程网络 DNS 后缀的请求路由到该网络中的自定义 DNS。

3. 在两个虚拟网络的自定义 DNS 服务器上，使用以下文本作为 `/etc/bind/named.conf.options` 文件的内容：

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver         
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
   将值 `10.0.0.0/16` 和 `10.1.0.0/16` 替换为虚拟网络的 IP 地址范围。 此条目允许每个网络中的资源发出 DNS 服务器请求。

    不是针对虚拟网络 DNS 后缀（例如，microsoft.com）的任何请求由 Azure 递归解析程序处理。

4. 若要使用此配置，请重启 Bind。 例如，在两个 DNS 服务器上运行 `sudo service bind9 restart`。

完成这些步骤后，就可以使用完全限定的域名 (FQDN) 连接到虚拟网络中的资源了。 现在可以将 HDInsight 安装到虚拟网络。

## <a name="next-steps"></a>后续步骤

* 有关将 HDInsight 配置为连接到本地网络的端到端示例，请参阅[将 HDInsight 连接到本地网络](./connect-on-premises-network.md)。
* 若要了解如何在 Azure 虚拟网络中配置 Apache HBase 群集，请参阅[在 Azure 虚拟网络中的 HDInsight 上创建 Apache HBase 群集](hbase/apache-hbase-provision-vnet.md)。
* 要了解如何配置 Apache HBase 异地复制，请参阅[在 Azure 虚拟网络中设置 Apache HBase 群集复制](hbase/apache-hbase-replication.md)。
* 有关 Azure 虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。

* 有关网络安全组的详细信息，请参阅[网络安全组](../virtual-network/security-overview.md)。

* 有关用户定义的路由的详细信息，请参阅[用户定义的路由和 IP 转发](../virtual-network/virtual-networks-udr-overview.md)。
