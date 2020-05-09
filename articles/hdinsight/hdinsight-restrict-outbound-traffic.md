---
title: 配置出站网络流量限制-Azure HDInsight
description: 了解如何配置 Azure HDInsight 群集的出站网络流量限制。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: eaf51f6778d38d236808c3fd809082bc3b2d54b2
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82863427"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>使用防火墙配置 Azure HDInsight 群集的出站网络流量

本文提供使用 Azure 防火墙保护来自 HDInsight 群集的出站流量的步骤。 以下步骤假设你要为现有群集配置 Azure 防火墙。 如果要在防火墙后部署新群集，请先创建 HDInsight 群集和子网。 然后按照本指南中的步骤进行操作。

## <a name="background"></a>背景

HDInsight 群集通常部署在虚拟网络中。 群集与该虚拟网络外部的服务具有依赖关系。

有多个依赖项需要入站流量。 无法通过防火墙设备发送入站管理流量。 此流量的源地址是已知的，已在[此处](hdinsight-management-ip-addresses.md)发布。 还可以使用此信息创建网络安全组 (NSG) 规则用于保护发往群集的入站流量。

HDInsight 出站流量依赖关系几乎完全定义为 Fqdn。 它们后面没有静态 IP 地址。 缺少静态地址意味着网络安全组（Nsg）无法锁定群集的出站流量。 地址经常更改，因此无法基于当前名称解析和使用来设置规则。

使用可以基于域名控制出站流量的防火墙保护出站地址。 Azure 防火墙基于目标或[fqdn 标记](../firewall/fqdn-tags.md)的 FQDN 限制出站流量。

## <a name="configuring-azure-firewall-with-hdinsight"></a>在 HDInsight 中配置 Azure 防火墙

使用 Azure 防火墙锁定现有 HDInsight 的传出流量的步骤摘要如下：

1. 创建子网。
1. 创建防火墙。
1. 将应用程序规则添加到防火墙
1. 将网络规则添加到防火墙。
1. 创建一个路由表。

### <a name="create-new-subnet"></a>创建新子网

在群集所在的虚拟网络中创建名为 **AzureFirewallSubnet** 的子网。

### <a name="create-a-new-firewall-for-your-cluster"></a>为群集创建新的防火墙

使用从教程**部署防火墙** [：使用 Azure 门户部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)中的步骤创建名为**FW01**的防火墙。

### <a name="configure-the-firewall-with-application-rules"></a>使用应用程序规则配置防火墙

创建一个应用程序规则集合，以允许群集发送和接收重要通信。

1. 在 Azure 门户中选择新防火墙 **Test-FW01**。

1. 导航到 "**设置** > **规则** > " "**应用程序规则集合** > **+ 添加应用程序规则集合**"。

    ![标题：添加应用程序规则集合](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. 在“添加应用程序规则集合”屏幕上提供以下信息：****

    **顶部部分**

    | properties|  值|
    |---|---|
    |名称| FwAppRule|
    |优先级|200|
    |操作|Allow|

    **FQDN 标记部分**

    | 名称 | 源地址 | FQDN 标记 | 说明 |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate 和 HDInsight | HDI 服务所需 |

    **目标 FQDN 部分**

    | 名称 | 源地址 | `Protocol:Port` | 目标 FQDN | 说明 |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | 允许 Windows 登录活动 |
    | Rule_3 | * | https:443 | login.microsoftonline.com | 允许 Windows 登录活动 |
    | Rule_4 | * | https:443,http:80 | storage_account_name。 core .net | 请将 `storage_account_name` 替换为实际的存储帐户名称。 如果群集由 WASB 提供支持，则为 WASB 添加一个规则。 若要仅使用 https 连接，请确保在存储帐户上启用了["需要安全传输"](../storage/common/storage-require-secure-transfer.md) 。 |

   ![标题：输入应用程序规则集合详细信息](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. 选择“添加”  。

### <a name="configure-the-firewall-with-network-rules"></a>使用网络规则配置防火墙

创建网络规则以正确配置 HDInsight 群集。

1. 继续之前的步骤，请导航到 "**网络规则集合** > **+ 添加网络规则集合**"。

1. 在“添加网络规则集合”屏幕上提供以下信息：****

    **顶部部分**

    | properties|  值|
    |---|---|
    |名称| FwNetRule|
    |优先级|200|
    |操作|Allow|

    **IP 地址部分**

    | 名称 | 协议 | 源地址 | 目标地址 | 目标端口 | 说明 |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | UDP | * | * | 123 | 时间服务 |
    | Rule_2 | Any | * | DC_IP_Address_1、DC_IP_Address_2 | * | 如果使用企业安全性套餐（ESP），请在 "IP 地址" 部分中添加一个网络规则，该规则允许与用于 ESP 群集的 AAD DS 通信。 可以在门户的 AAD-DS 部分找到域控制器的 IP 地址 |
    | Rule_3 | TCP | * | Data Lake Storage 帐户的 IP 地址 | * | 如果使用的是 Azure Data Lake Storage，则可以在“IP 地址”部分中添加一个网络规则来解决 ADLS Gen1 和 Gen2 的 SNI 问题。 此选项会将流量路由到防火墙。 这可能会导致较大的数据负载成本，但会在防火墙日志中记录和审核流量。 确定 Data Lake Storage 帐户的 IP 地址。 可以使用 PowerShell 命令（如） `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")`将 FQDN 解析为 IP 地址。|
    | Rule_4 | TCP | * | * | 12000 | （可选）如果使用的是 Log Analytics，请在“IP 地址”部分中创建一个网络规则以实现与 Log Analytics 工作区的通信。 |

    **服务标记部分**

    | 名称 | 协议 | 源地址 | 服务标记 | 目标端口 | 说明 |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | 在 SQL 的 "服务标记" 部分中配置网络规则，该规则将允许您记录和审核 SQL 流量。 除非已为 HDInsight 子网上的 SQL Server 配置服务终结点，否则将绕过防火墙。 |

   ![标题：输入应用程序规则集合](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. 选择“添加”  。

### <a name="create-and-configure-a-route-table"></a>创建并配置路由表

创建包含以下条目的路由表：

* [运行状况和管理服务](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions)中的所有 IP 地址：下一跃点类型为**Internet**的所有区域。

* 在[运行状况和管理服务](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions)中创建群集的区域的两个 IP 地址：具有 "下一跃点" 类型**Internet**的特定区域。

* IP 地址 0.0.0.0/0 的一个虚拟设备路由，其下一跃点为 Azure 防火墙专用 IP 地址。

例如，若要为“美国东部”区域创建的群集配置路由表，请使用以下步骤:

1. 选择 Azure 防火墙 **Test-FW01**。 复制“概述”页上列出的“专用 IP 地址”。******** 本示例使用**示例地址 10.0.2.4**。

1. 然后导航到**所有服务** > **网络** > **路由表**并**创建路由表**。

1. 在新路由中，导航到 "**设置** > " "路由" "**路由** > " "**添加**"。 添加以下路由：

| 路由名称 | 地址前缀 | 下一跃点类型 | 下一跃点地址 |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | 不可用 |
| 23.99.5.239 | 23.99.5.239/32 | Internet | 不可用 |
| 168.61.48.131 | 168.61.48.131/32 | Internet | 不可用 |
| 138.91.141.162 | 138.91.141.162/32 | Internet | 不可用 |
| 13.82.225.233 | 13.82.225.233/32 | Internet | 不可用 |
| 40.71.175.99 | 40.71.175.99/32 | Internet 的虚拟网络适配器 | NA |
| 0.0.0.0 | 0.0.0.0/0 | 虚拟设备 | 10.0.2.4 |

完成路由表配置：

1. 选择“设置”下的“子网”，将创建的路由表分配到 HDInsight 子网。********

1. 选择 " **+ 关联**"。

1. 在 "**关联子网**" 屏幕上，选择你的群集创建到的虚拟网络。 以及用于 HDInsight 群集的**子网**。

1. 选择“确定”  。

## <a name="edge-node-or-custom-application-traffic"></a>边缘节点或自定义应用程序流量

上述步骤可让群集正常运行。 但在可能的情况下，你仍然需要配置依赖项，以适应边缘节点上运行的自定义应用程序。

必须识别应用程序依赖项并将其添加到 Azure 防火墙或路由表。

必须为应用程序流量创建路由，以避免非对称路由问题。

如果应用程序有其他依赖项，则需要将这些依赖项添加到 Azure 防火墙。 创建允许 HTTP/HTTPS 流量的应用程序规则，并针对其他方面的控制创建网络规则。

## <a name="logging-and-scale"></a>日志记录和缩放

Azure 防火墙可将日志发送到一些不同的存储系统。 有关为防火墙配置日志记录的说明，请按照[教程：监视 Azure 防火墙日志和指标](../firewall/tutorial-diagnostics.md)中的步骤操作。

完成日志记录设置后，如果使用的是 Log Analytics，则可以使用以下查询查看已阻止的流量：

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

首次使用应用程序时，将 Azure 防火墙与 Azure Monitor 日志集成非常有用。 尤其是当你不知道所有应用程序依赖项时。 可以通过[在 Azure Monitor 中分析日志数据](../azure-monitor/log-query/log-query-overview.md)详细了解 Azure Monitor 日志

若要了解 Azure 防火墙的缩放限制以及如何提高请求，请参阅[此文档](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)或参阅[常见问题解答](../firewall/firewall-faq.md)。

## <a name="access-to-the-cluster"></a>访问群集

成功设置防火墙后，可以使用内部终结点（`https://CLUSTERNAME-int.azurehdinsight.net`）从虚拟网络内部访问 Ambari。

若要使用公共终结点 (`https://CLUSTERNAME.azurehdinsight.net`) 或 SSH 终结点 (`CLUSTERNAME-ssh.azurehdinsight.net`)，请确保路由表和 NSG 规则中包含适当的路由，以避免出现[此处](../firewall/integrate-lb.md)所述的非对称路由问题。 具体而言，在这种情况下，需要允许入站 NSG 规则中的客户端 IP 地址，并在将下一跃点设置为 `internet` 的情况下，将此地址添加到用户定义的路由表中。 如果未正确设置路由，你会看到超时错误。

## <a name="next-steps"></a>后续步骤

* [Azure HDInsight 虚拟网络体系结构](hdinsight-virtual-network-architecture.md)
* [配置网络虚拟设备](./network-virtual-appliance.md)