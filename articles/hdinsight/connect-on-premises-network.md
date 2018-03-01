---
title: "将 HDInsight 连接到本地网络 - Azure HDInsight | Microsoft Docs"
description: "了解如何在 Azure 虚拟网络中创建 HDInsight 群集，并将其连接到本地网络。 了解如何使用自定义 DNS 服务器配置 HDInsight 与本地网络之间的名称解析。"
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/23/2018
ms.author: larryfr
ms.openlocfilehash: 03214f25858ae340908a1d1b7f3ff7f62d545dc9
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="connect-hdinsight-to-your-on-premise-network"></a>将 HDInsight 连接到本地网络

了解如何使用 Azure 虚拟网络和 VPN 网关将 HDInsight 连接到本地网络。 本文档提供以下相关规划信息：

* 在连接到本地网络的 Azure 虚拟网络上使用 HDInsight。

* 配置虚拟网络与本地网络之间的 DNS 名称解析。

* 将网络安全组配置为限制对 HDInsight 的 Internet 访问。

* 虚拟网络上由 HDInsight 提供的端口。

## <a name="create-the-virtual-network-configuration"></a>创建虚拟网络配置

使用以下文档来了解如何创建连接到本地网络的 Azure 虚拟网络：
    
* [使用 Azure 门户](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

* [使用 Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

* [使用 Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="configure-name-resolution"></a>配置名称解析

若要允许联接网络中的 HDInsight 和资源通过名称进行通信，必须执行以下操作：

* 在 Azure 虚拟网络中创建自定义 DNS 服务器。

* 配置虚拟网络以使用自定义 DNS 服务器，而不是默认 Azure 递归解析程序。

* 配置自定义 DNS 服务器和本地 DNS 服务器之间的转发。

此配置可实现以下行为：

* 对虚拟网络具有 DNS 后缀的完全限定域名的请求已转发至自定义 DNS 服务器。 然后，自定义 DNS 服务器将这些请求转发至 Azure 递归解析程序，Azure 递归解析程序将返回 IP 地址。

* 其他所有请求将转发至本地 DNS 服务器。 甚至对公共 Internet 资源（如 microsoft.com）的请求也将因名称解析转发至本地 DNS 服务器。

在下面的关系图中，绿线表示以虚拟网络的 DNS 后缀结尾的资源请求。 蓝线表示本地网络或公共 Internet 上的资源请求。

![本文档将使用如何解析配置中的 DNS 请求的关系图](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

### <a name="create-a-custom-dns-server"></a>创建自定义 DNS 服务器

> [!IMPORTANT]
> 在将 HDInsight 安装到虚拟网络之前，必须先创建和配置 DNS 服务器。

若要创建使用 [Bind](https://www.isc.org/downloads/bind/) DNS 软件的 Linux VM，请使用以下步骤：

> [!NOTE]
> 以下步骤将通过 [Azure 门户](https://portal.azure.com)创建 Azure 虚拟机。 有关创建虚拟机的其他方法，请参阅以下文档：
>
> * [创建 VM - Azure CLI](../virtual-machines/linux/quick-create-cli.md)
> * [创建 VM - Azure PowerShell](../virtual-machines/linux/quick-create-portal.md)

1. 在 [Azure 门户](https://portal.azure.com)中，依次选择“+”、“计算”和“Ubuntu Server 16.04 LTS”。

    ![创建 Ubuntu 虚拟机](./media/connect-on-premises-network/create-ubuntu-vm.png)

2. 在“基本信息”部分输入以下信息：

    * 名称：用于标识该虚拟机的友好名称。 例如，DNSProxy。
    * 用户名：SSH 帐户的名称。
    * SSH 公钥或密码：SSH 帐户的身份验证方法。 建议使用公钥，因为公钥更安全。 有关详细信息，请参阅[创建并使用用于 Linux VM 的 SSH 密钥](../virtual-machines/linux/mac-create-ssh-keys.md)文档。
    * 资源组：选择“使用现有”，然后选择包含前面所建虚拟网络的资源组。
    * 位置：选择与虚拟网络相同的位置。

    ![虚拟机基础配置](./media/connect-on-premises-network/vm-basics.png)

    将其他项保留为默认值，然后选择“确定”。

3. 在“选择大小”部分，选择 VM 大小。 对于本教程，请选择最小和最低成本选项。 若要继续，请使用“选择”按钮。

4. 在“设置”部分输入以下信息：

    * 虚拟网络：选择前面创建的虚拟网络。

    * 子网：选择虚拟网络的默认子网。 请勿选择 VPN 网关所用的子网。

    * 诊断存储帐户：选择一个现有存储帐户，或创建一个新的存储帐户。

    ![虚拟网络设置](./media/connect-on-premises-network/virtual-network-settings.png)

    将其他项保留为默认值，然后选择“确定”以继续。

5. 在“购买”部分，选择“购买”按钮可创建虚拟机。

6. 创建虚拟机后，即会显示其“概述”部分。 在左侧的列表中，选择“属性”。 保存公共 IP 地址和专用 IP 地址值。 该值将在下节中使用。

    ![公共和专用 IP 地址](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>安装和配置 Bind（DNS 软件）

1. 使用 SSH 连接到虚拟机的公共 IP 地址。 以下示例将在 40.68.254.142 连接到虚拟机：

    ```bash
    ssh sshuser@40.68.254.142
    ```

    将 `sshuser` 替换为创建群集时指定的 SSH 用户帐户。

    > [!NOTE]
    > 可通过多种方法获取 `ssh` 实用工具。 在 Linux、Unix 和 macOS 上，操作系统会附带此实用工具。 如果使用的是 Windows，请考虑以下选项之一：
    >
    > * [Azure Cloud Shell](../cloud-shell/quickstart.md)
    > * [Windows 10 版 Bash on Ubuntu](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. 若要安装 Bind，请从 SSH 会话中使用以下命令：

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. 若要配置 Bind 将名称解析请求转发至本地 DNS 服务器，请使用以下文本作为 `/etc/bind/named.conf.options` 文件的内容：

        acl goodclients {
            10.0.0.0/16; # Replace with the IP address range of the virtual network
            10.1.0.0/16; # Replace with the IP address range of the on-premises network
            localhost;
            localnets;
        };

        options {
                directory "/var/cache/bind";

                recursion yes;

                allow-query { goodclients; };

                forwarders {
                192.168.0.1; # Replace with the IP address of the on-premises DNS server
                };

                dnssec-validation auto;

                auth-nxdomain no;    # conform to RFC1035
                listen-on { any; };
        };

    > [!IMPORTANT]
    > 将 `goodclients` 部分中的值替换为虚拟网络和本地网络的 IP 地址范围。 本部分定义此 DNS 服务器从其中接受请求的地址。
    >
    > 将 `forwarders` 部分中的 `192.168.0.1` 条目替换为本地 DNS 服务器的 IP 地址。 此项可将 DNS 请求路由到本地 DNS 服务器进行解析。

    若要编辑此文件，请使用以下命令：

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    若要保存文件，请使用 Ctrl+X、Ctrl+Y，然后按 Enter。

4. 在 SSH 会话中，请使用以下命令：

    ```bash
    hostname -f
    ```

    此命令会返回类似于以下文本的值：

        dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` 文本是该虚拟网络的 DNS 后缀。 请保存该值，因为稍后将使用它。

5. 若要配置 Bind 以在虚拟网络中解析资源的 DNS 名称，请使用以下文本作为 `/etc/bind/named.conf.local` 文件的内容：

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]
    > 必须将 `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` 替换为之前检索到的 DNS 后缀。

    若要编辑此文件，请使用以下命令：

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    若要保存文件，请使用 Ctrl+X、Ctrl+Y，然后按 Enter。

6. 若要启动 Bind，请使用以下命令：

    ```bash
    sudo service bind9 restart
    ```

7. 若要验证该绑定是否可以解析本地网络中的资源名称，请使用以下命令：

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]
    > 将 `dns.mynetwork.net` 替换为本地网络中的资源的完全限定的域名 (FQDN)。
    >
    > 将 `10.0.0.4` 替换为虚拟网络中自定义 DNS 服务器的内部 IP 地址。

    显示的响应如下文所示：

        Server:         10.0.0.4
        Address:        10.0.0.4#53

        Non-authoritative answer:
        Name:   dns.mynetwork.net
        Address: 192.168.0.4

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>配置虚拟网络以使用自定义 DNS 服务器

若要配置虚拟网络以使用自定义 DNS 服务器，而不是 Azure 递归解析程序，请使用以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“虚拟网络”，然后选择“DNS 服务器”。

2. 选择“自定义”，然后输入自定义 DNS 服务器的内部 IP 地址。 最后，选择“保存”。

    ![为网络设置自定义 DNS 服务器](./media/connect-on-premises-network/configure-custom-dns.png)

### <a name="configure-the-on-premises-dns-server"></a>配置本地 DNS 服务器

在上一节中，已配置自定义 DNS 服务器，以将请求转发到本地 DNS 服务器。 下一步，必须配置本地 DNS 服务器，以将请求转发到自定义 DNS 服务器。

有关如何配置 DNS 服务器的具体步骤，请参考 DNS 服务器软件文档。 查找如何配置条件转发器的步骤。

条件转发仅转发针对特定 DNS 后缀的请求。 在这种情况下，必须针对虚拟网络的 DNS 后缀配置转发器。 此后缀的请求应该转发到自定义 DNS 服务器的 IP 地址。 

下面的文本是关于 Bind DNS 软件的条件转发器配置的示例：

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

有关对 Windows Server 2016 使用 DNS 的信息，请参阅 [Add-DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone) 文档...

配置本地 DNS 服务器后，可以从本地网络使用 `nslookup` 来验证是否可以解析虚拟网络中的名称。 以下示例 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

此示例在 196.168.0.4 使用本地 DNS 服务器来解析自定义 DNS 服务器的名称。 将 IP 地址替换为本地 DNS 服务器的 IP 地址。 将 `dnsproxy` 地址替换为自定义 DNS 服务器完全限定的域名。

## <a name="optional-control-network-traffic"></a>可选：控制网络流量

可以使用网络安全组 (NSG) 或用户定义路由 (UDR) 控制网络流量。 NSG 允许筛选入站和出站流量以及允许或拒绝流量。 通过 UDR 可控制流量在虚拟网络、Internet 和本地网络中的资源间的流动方式。

> [!WARNING]
> HDInsight 需要来自 Azure 云中的特定 IP 地址的入站访问权限以及不受限制的出站访问权限。 如果使用 NSG 或 UDR 来控制流量，必须执行以下步骤：
>
> 1. 查找包含虚拟网络的位置的 IP 地址。 若要获取按位置划分的所需 IP 列表，请参阅[所需 IP 地址](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip)。
>
> 2. 允许来自 IP 地址的入站流量。
>
>    * NSG：允许端口 443 来自 Internet 的入站流量。
>    * UDR：将路由的“下一个跃点”类型设为 Internet。

有关使用 Azure PowerShell 或 Azure CLI 创建 NSG 的示例，请参阅[使用 Azure 虚拟网络扩展 HDInsight](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg) 文档。

## <a name="create-the-hdinsight-cluster"></a>创建 HDInsight 群集

> [!WARNING]
> 在将 HDInsight 安装到虚拟网络之前，必须先配置自定义 DNS 服务器。

使用[通过 Azure 门户创建 HDInsight 群集](./hdinsight-hadoop-create-linux-clusters-portal.md)文档中的步骤来创建 HDInsight 群集。

> [!WARNING]
> * 在群集创建过程中，必须选择包含虚拟网络的位置。
>
> * 在配置的高级设置部分，必须选择前面创建的虚拟网络和子网。

## <a name="connecting-to-hdinsight"></a>连接到 HDInsight

关于 HDInsight 的大多数文档都假定能够通过 Internet 访问群集。 例如，可在 https://CLUSTERNAME.azurehdinsight.net 连接到群集。 此地址使用公共网关，在已使用 NSG 或 UDR 限制从 Internet 访问时不可用。

一些文档在通过 SSH 会话连接到群集时还引用了 `headnodehost`。 该地址仅可在群集中的节点上使用，在通过虚拟网络连接的客户端上不可用。

若要通过虚拟网络直接连接到 HDInsight，请使用以下步骤：

1. 若要发现 HDInsight 群集节点的内部完全限定域名，请使用以下一种方法：

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

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

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. 若要确定服务的可用端口，请参阅 [HDInsight 的 Hadoop 服务所用的端口](./hdinsight-hadoop-port-settings-for-services.md)文档。

    > [!IMPORTANT]
    > 一些在头节点上托管的服务一次只能在一个节点上处于活动状态。 如果在一个头节点上尝试访问服务并失败，请切换到其他头节点。
    >
    > 例如，Ambari 一次仅在一个头节点上处于活动状态。 如果在一个头节点上尝试访问 Ambari 并返回 404 错误，则它正在其他头节点上运行。

## <a name="next-steps"></a>后续步骤

* 有关在 Azure 虚拟网络中使用 HDInsight 的详细信息，请参阅[使用 Azure 虚拟网络扩展 HDInsight](./hdinsight-extend-hadoop-virtual-network.md)。

* 有关 Azure 虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。

* 有关网络安全组的详细信息，请参阅[网络安全组](../virtual-network/virtual-networks-nsg.md)。

* 有关用户定义的路由的详细信息，请参阅[用户定义第路由和 IP 转发](../virtual-network/virtual-networks-udr-overview.md)。
