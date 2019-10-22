---
title: 为 Azure HDInsight 群集配置出站网络流量限制
description: 了解如何配置 Azure HDInsight 群集的出站网络流量限制。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 39a7e78085f297838a028489de23c1991b6d672f
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693430"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall-preview"></a>使用防火墙配置 Azure HDInsight 群集的出站网络流量（预览）

本文提供使用 Azure 防火墙保护 HDInsight 群集中的出站流量的步骤。 以下步骤假设你要为现有群集配置 Azure 防火墙。 如果要部署新群集并在防火墙后面创建 HDInsight 群集和子网，请先创建 HDInsight 群集和子网，然后按照本指南中的步骤进行操作。

## <a name="background"></a>背景

Azure HDInsight 群集通常部署在自己的虚拟网络中。 群集与该虚拟网络外部的服务具有依赖关系，这些服务需要网络访问权限才能正常运行。

有几个依赖项需要入站流量。 无法通过防火墙设备发送入站管理流量。 此流量的源地址已知，并在[此处](hdinsight-management-ip-addresses.md)发布。 你还可以创建包含此信息的网络安全组（NSG）规则，以确保到群集的入站流量的安全。

HDInsight 出站通信依赖项几乎使用 Fqdn 完全定义，后者后面没有静态 IP 地址。 缺少静态地址意味着网络安全组（Nsg）不能用于锁定群集的出站流量。 通常，地址会发生更改，而不能根据当前名称解析设置规则并使用它来设置 NSG 规则。

保护出站地址的解决方案是使用防火墙设备，该设备可以基于域名控制出站流量。 Azure 防火墙可以基于目标或[fqdn 标记](https://docs.microsoft.com/azure/firewall/fqdn-tags)的 FQDN 限制出站 HTTP 和 HTTPS 流量。

## <a name="configuring-azure-firewall-with-hdinsight"></a>配置 HDInsight 的 Azure 防火墙

使用 Azure 防火墙锁定现有 HDInsight 中的出口的步骤摘要如下：
1. 创建防火墙。
1. 向防火墙添加应用程序规则
1. 向防火墙添加网络规则。
1. 创建一个路由表。

### <a name="create-a-new-firewall-for-your-cluster"></a>为群集创建新的防火墙

1. 在群集所在的虚拟网络中创建名为**AzureFirewallSubnet**的子网。 
1. 使用教程中的步骤创建新的防火墙**FW01** [：使用 Azure 门户部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)。

### <a name="configure-the-firewall-with-application-rules"></a>用应用程序规则配置防火墙

创建允许群集发送和接收重要通信的应用程序规则集合。

从 Azure 门户中选择新的防火墙**FW01** 。 单击 "**设置**" 下的**规则** > **应用程序规则集合** > **添加应用程序规则集合**。

![标题：添加应用程序规则集合](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

在 "**添加应用程序规则集合**" 屏幕上，完成以下步骤：

1. 输入**名称**、**优先级**，然后在 "**操作**" 下拉菜单中单击 "**允许**"，并在**FQDN 标记部分**输入以下规则：

   | 名称 | **源地址** | **FQDN 标记** | **说明** |
   | --- | --- | --- | --- |
   | Rule_1 | * | HDInsight 和 Windowsupdate.log | HDI services 必需的 |

1. 将以下规则添加到 "**目标 fqdn" 部分**：

   | 名称 | **源地址** | **协议：端口** | **目标 FQDN** | **说明** |
   | --- | --- | --- | --- | --- |
   | Rule_2 | * | https：443 | login.windows.net | 允许 Windows 登录活动 |
   | Rule_3 | * | https：443 | login.microsoftonline.com | 允许 Windows 登录活动 |
   | Rule_4 | * | https：443，http：80 | < storage_account_name （.net） > | 如果你的群集支持 WASB，则添加 WASB 的规则。 若要仅使用 https 连接，请确保在存储帐户上启用了["需要安全传输"](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) 。 |

1. 单击“添加”。

   ![标题：输入应用程序规则集合详细信息](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>配置带网络规则的防火墙

创建网络规则以正确配置 HDInsight 群集。

1. 从 Azure 门户中选择新的防火墙**FW01** 。
1. 单击 "**设置**" 下的 "**规则** > **网络规则集合** > **添加网络规则集合**。
1. 在 "**添加网络规则集合**" 屏幕上，输入**名称**、**优先级**，并单击 "**操作**" 下拉菜单中的 "**允许**"。
1. 在 " **IP 地址**" 部分中创建以下规则：

   | 名称 | 协议 | **源地址** | **目标地址** | **目标端口** | **说明** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_1 | UDP | * | * | `123` | 时间服务 |
   | Rule_2 | 任意 | * | DC_IP_Address_1, DC_IP_Address_2 | `*` | 如果使用企业安全性套餐（ESP），请在 "IP 地址" 部分中添加一个网络规则，该规则允许与用于 ESP 群集的 AAD DS 通信。 可以在门户的 "AAD-DS" 部分找到域控制器的 IP 地址 | 
   | Rule_3 | TCP | * | Data Lake Storage 帐户的 IP 地址 | `*` | 如果使用 Azure Data Lake Storage，则可以在 "IP 地址" 部分中添加一个网络规则，以解决与 ADLS Gen1 和 Gen2 的 SNI 问题。 此选项会将流量路由到防火墙，这可能会导致较大的数据负载开销，但会在防火墙日志中记录和审核流量。 确定 Data Lake Storage 帐户的 IP 地址。 可以使用 powershell 命令（如 `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")`）将 FQDN 解析为 IP 地址。|
   | Rule_4 | TCP | * | * | `12000` | 可有可无如果使用 Log Analytics，请在 "IP 地址" 部分中创建网络规则，以启用与 Log Analytics 工作区的通信。 |

1. 在 "**服务标记**" 部分中创建以下规则：

   | 名称 | 协议 | **源地址** | **服务标记** | **目标端口** | **说明** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_7 | TCP | * | SQL | `1433` | 在 "服务标记" 部分中为 SQL 配置一个网络规则，该规则将允许你记录和审核 SQL 流量，除非你在 HDInsight 子网中为 SQL Server 配置了服务终结点，这样将绕过防火墙。 |

1. 单击 "**添加**" 以完成创建网络规则集合。

   ![标题：输入应用程序规则集合](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>创建和配置路由表

使用以下项创建路由表：

1. [此所需 HDInsight 管理 IP 地址列表](../hdinsight/hdinsight-management-ip-addresses.md)中的六个地址，下一跃点**Internet**：
    1. 所有区域中所有群集的四个 IP 地址
    1. 用于创建群集的区域的两个 IP 地址
1. 一个虚拟设备路由 IP 地址 0.0.0.0/0，下一跃点是你的 Azure 防火墙专用 IP 地址。

例如，若要为 "美国中部" 区域中创建的群集配置路由表，请执行以下步骤：

1. 登录到 Azure 门户。
1. 选择 Azure 防火墙**FW01**。 复制 "**概述**" 页上列出的**专用 IP 地址**。 在此示例中，我们将使用**10.1.1.4 的示例地址**
1. 创建新的路由表。
1. 单击 "**设置**" 下的 "**路由**"。
1. 单击 "**添加**" 以创建下表中的 IP 地址的路由。

| 路由名称 | 地址前缀 | 下一跃点类型 | 下一跃点地址 |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | NA |
| 23.99.5.239 | 23.99.5.239/32 | Internet | NA |
| 168.61.48.131 | 168.61.48.131/32 | Internet | NA |
| 138.91.141.162 | 138.91.141.162/32 | Internet | NA |
| 13.67.223.215 | 13.67.223.215/32 | Internet | NA |
| 40.86.83.253 | 40.86.83.253/32 | Internet | NA |
| 0.0.0.0 | 0.0.0.0/0 | 虚拟设备 | 10.1.1.4 |

完成路由表配置：

1. 单击 "**设置**" 下的 "**子**网"，将创建的路由表分配给 HDInsight 子网，然后单击 "**关联**"。
1. 在 "**关联子网**" 屏幕上，选择群集创建到的虚拟网络，以及用于 hdinsight 群集的**hdinsight 子网**。
1. 单击 **“确定”** 。

## <a name="edge-node-or-custom-application-traffic"></a>边缘节点或自定义应用程序流量

以上步骤将允许群集正常运行。 你仍需要配置依赖项以适应在边缘节点上运行的自定义应用程序（如果适用）。

必须标识应用程序依赖关系并将其添加到 Azure 防火墙或路由表。

必须为应用程序流量创建路由，以避免非对称路由问题。

如果你的应用程序具有其他依赖项，则需要将其添加到你的 Azure 防火墙。 创建允许 HTTP/HTTPS 流量的应用程序规则，并针对其他方面的控制创建网络规则。

## <a name="logging-and-scale"></a>日志记录和缩放

Azure 防火墙可以将日志发送到几个不同的存储系统。 有关为防火墙配置日志记录的说明，请按照[教程：监视 Azure 防火墙日志和指标](../firewall/tutorial-diagnostics.md)中的步骤操作。

完成日志记录设置后，如果要将数据记录到 Log Analytics，则可以使用如下所示的查询查看已阻止的流量：

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

当你不知道所有应用程序依赖项时，当首次让应用程序正常工作时，将 Azure 防火墙与 Azure Monitor 日志集成非常有用。 可以通过[在 Azure Monitor 中分析日志数据](../azure-monitor/log-query/log-query-overview.md)详细了解 Azure Monitor 日志

若要了解 Azure 防火墙和请求增加的规模限制，请参阅[此](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#azure-firewall-limits)文档。

## <a name="access-to-the-cluster"></a>对群集的访问权限
成功设置防火墙后，可以使用内部终结点（`https://<clustername>-int.azurehdinsight.net`）从 VNET 内部访问 Ambari。 

若要使用公共终结点（`https://<clustername>.azurehdinsight.net`）或 ssh 终结点（`<clustername>-ssh.azurehdinsight.net`），请确保路由表和 NSG 规则中具有正确的路由，以避免[此处](https://docs.microsoft.com/azure/firewall/integrate-lb)所述的非对称路由问题。 具体而言，在这种情况下，你需要允许入站 NSG 规则中的客户端 IP 地址，并将其添加到将下一个跃点设置为 `internet` 的用户定义的路由表。 如果未正确设置，将看到超时错误。

## <a name="configure-another-network-virtual-appliance"></a>配置另一网络虚拟设备

>[!Important]
> 仅当要配置除 Azure 防火墙以外的网络虚拟设备（NVA）时，**才**需要以下信息。

上述说明可帮助你配置 Azure 防火墙，以限制来自 HDInsight 群集的出站流量。 Azure 防火墙自动配置为允许很多常见重要方案的流量。 如果要使用其他网络虚拟设备，则需要手动配置多个附加功能。 配置网络虚拟设备时，请注意以下事项：

* 应在支持服务终结点的服务中配置服务终结点。
* IP 地址依赖关系适用于非 HTTP/S 流量（TCP 和 UDP 通信）。
* 可以在 NVA 设备中放置 FQDN HTTP/HTTPS 终结点。
* 通配符 HTTP/HTTPS 终结点是可根据多个限定符变化的依赖项。
* 将创建的路由表分配给 HDInsight 子网。

### <a name="service-endpoint-capable-dependencies"></a>支持服务终结点的依赖项

| **终结点** |
|---|
| Azure SQL |
| Azure 存储器 |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>IP 地址依赖项

| **终结点** | **详细信息** |
|---|---|
| \*:123 | NTP 时钟检查。 在端口 123 上的多个终结点中检查流量 |
| [此处](hdinsight-management-ip-addresses.md)发布的 ip | 这些是 HDInsight 服务 |
| 用于 ESP 群集的 AAD-DS 专用 Ip |
| \*：16800，用于 KMS Windows 激活 |
| Log Analytics 的 \*12000 |

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 依赖项

>[!Important]
> 以下列表仅提供了几个最重要的 Fqdn。 可以[在此文件中](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)获取用于配置 NVA 的 fqdn 的完整列表。

| **终结点**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                |
| ocsp.digicert.com:80                                                |

## <a name="next-steps"></a>后续步骤

* [Azure HDInsight 虚拟网络体系结构](hdinsight-virtual-network-architecture.md)
