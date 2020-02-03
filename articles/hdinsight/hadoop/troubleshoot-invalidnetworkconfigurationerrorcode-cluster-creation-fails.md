---
title: InvalidNetworkConfigurationErrorCode 错误-Azure HDInsight
description: 在 Azure HDInsight 中通过 InvalidNetworkConfigurationErrorCode 创建失败的群集的各种原因
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 6dd4db999cb130c9816ad023888a4333e968c224
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720378"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>在 Azure HDInsight 中创建群集失败，出现 InvalidNetworkConfigurationErrorCode

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

如果看到错误代码 `InvalidNetworkConfigurationErrorCode` 说明 "虚拟网络配置与 HDInsight 要求不兼容"，则通常表示群集的[虚拟网络配置](../hdinsight-plan-virtual-network-deployment.md)存在问题。 根据错误说明的其余部分，按照以下部分解决你的问题。

## <a name="hostname-resolution-failed"></a>"主机名解析失败"

### <a name="issue"></a>问题

错误说明包含 "主机名解析失败"。

### <a name="cause"></a>原因

此错误指向自定义 DNS 配置问题。 虚拟网络中的 DNS 服务器可以将 DNS 查询转发到 Azure 的递归解析程序，以便解析该虚拟网络中的主机名（有关详细信息，请参阅[虚拟网络中的名称解析](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)）。 可以通过虚拟 IP 168.63.129.16 访问 Azure 的递归解析程序。 此 IP 只能从 Azure Vm 进行访问。 如果你使用的是 OnPrem DNS 服务器，或者你的 DNS 服务器是 Azure VM，而这不是群集的虚拟网络的一部分，则它将不起作用。

### <a name="resolution"></a>解决方法

1. 通过 Ssh 连接到作为群集一部分的 VM，并运行命令 `hostname -f`。 这会返回主机的完全限定域名（在以下说明中称为 `<host_fqdn>`）。

1. 然后，运行命令 `nslookup <host_fqdn>` （例如，`nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`）。 如果此命令将名称解析为 IP 地址，则表示你的 DNS 服务器工作正常。 在这种情况下，使用 HDInsight 引发支持案例，我们将调查你的问题。 在支持案例中，请包括所执行的故障排除步骤。 这可以帮助我们更快地解决问题。

1. 如果上面的命令未返回 IP 地址，请运行 `nslookup <host_fqdn> 168.63.129.16` （例如，`nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`）。 如果此命令能够解析 IP，则意味着 DNS 服务器不会将查询转发到 Azure 的 DNS，或者它不是与群集处于同一虚拟网络中的 VM。

1. 如果 Azure VM 不能充当群集的虚拟网络中的自定义 DNS 服务器，则需要先添加此项。 在虚拟网络中创建一个 VM，该 VM 将配置为 DNS 转发器。

1. 在虚拟网络中部署了 VM 后，请在此 VM 上配置 DNS 转发规则。 将所有 Idn 名称解析请求转发到168.63.129.16，将其余的请求转发到你的 DNS 服务器。 [下面](../hdinsight-plan-virtual-network-deployment.md)是用于自定义 DNS 服务器的此设置的示例。

1. 添加此 VM 的 IP 地址作为虚拟网络 DNS 配置的第一个 DNS 条目。

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"无法连接到 Azure 存储帐户"

### <a name="issue"></a>问题

错误说明包含 "无法连接到 Azure 存储帐户" 或 "无法连接到 Azure SQL"。

### <a name="cause"></a>原因

Azure 存储和 SQL 没有固定 IP 地址，因此，我们需要允许与所有 Ip 的出站连接，以允许访问这些服务。 具体的解决步骤取决于您是否设置了网络安全组（NSG）或用户定义的规则（UDR）。 有关这些配置的详细信息，请参阅有关利用[HDInsight 和网络安全组和用户定义的路由控制网络流量](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)的部分。

### <a name="resolution"></a>解决方法

* 如果群集使用[网络安全组（NSG）](../../virtual-network/virtual-network-vnet-plan-design-arm.md)。

    中转到 Azure 门户，并确定与部署群集的子网关联的 NSG。 在 "**出站安全规则**" 部分中，允许无限制地对 internet 进行出站访问（请注意，此处的**优先级**越小，优先级越高）。 同时，在 "**子网**" 部分中，确认此 NSG 是否应用于群集子网。

* 如果群集使用[用户定义的路由（UDR）](../../virtual-network/virtual-networks-udr-overview.md)。

    请中转到 Azure 门户并标识与正在部署群集的子网关联的路由表。 找到子网的路由表后，请检查其中的**路由**部分。

    如果已定义路由，请确保已在其中部署了群集的区域的 IP 地址的路由，并且每个路由的**NextHopType**为**Internet**。 应该为上述文章中记录的每个所需的 IP 地址定义一个路由。

---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"虚拟网络配置与 HDInsight 要求不兼容"

### <a name="issue"></a>问题

错误说明包含类似如下所示的消息：

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>原因

自定义 DNS 设置可能存在问题。

### <a name="resolution"></a>解决方法

验证168.63.129.16 是否在自定义 DNS 链中。 虚拟网络中的 DNS 服务器可以将 DNS 查询转发到 Azure 的递归解析程序，以便解析该虚拟网络中的主机名。 有关详细信息，请参阅[虚拟网络中的名称解析](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。 可以通过虚拟 IP 168.63.129.16 访问 Azure 的递归解析程序。

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)连接到群集。 将 CLUSTERNAME 替换为群集名称，然后输入以下命令，以编辑以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 执行以下命令：

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    应看到与下面类似的内容：

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    根据结果，选择以下步骤之一：

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 不在此列表中

**选项 1**  
按照[规划 Azure HDInsight 的虚拟网络](../hdinsight-plan-virtual-network-deployment.md)中所述的步骤，将168.63.129.16 添加为虚拟网络的第一个自定义 DNS。 这些步骤仅适用于在 Linux 上运行的自定义 DNS 服务器。

**选项 2**  
为虚拟网络部署 DNS 服务器 VM。 这包括以下步骤：

* 在虚拟网络中创建一个 VM，该 VM 将配置为 DNS 转发器（它可以是 Linux 或 windows VM）。
* 在此 VM 上配置 DNS 转发规则（将所有 Idn 名称解析请求转发到168.63.129.16，将其余的请求转发到你的 DNS 服务器）。
* 添加此 VM 的 IP 地址作为虚拟网络 DNS 配置的第一个 DNS 条目。

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 在列表中

在这种情况下，请使用 HDInsight 创建支持案例，我们将调查你的问题。 在支持案例中包括以下命令的结果。 这可以帮助我们更快地调查和解决问题。

在头节点上的 ssh 会话中，编辑，然后运行以下内容：

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```

---

### <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -通过将 Azure 社区连接到适当的资源来改进客户体验的官方 Microsoft Azure 帐户：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
