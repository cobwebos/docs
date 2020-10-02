---
title: 在 Azure HDInsight 中配置网络虚拟设备
description: 了解如何在 Azure HDInsight 中为网络虚拟设备配置一些其他功能。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 407160a5c315844003db4c5e371a03e6e25d2694
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630927"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>在 Azure HDInsight 中配置网络虚拟设备

> [!Important]
> **仅当**所要配置的网络虚拟设备 (NVA) 不是 Azure 防火墙时，才需要以下信息。

对于许多常见的重要方案，Azure 防火墙已自动配置为允许流量。 使用另一个网络虚拟设备将需要配置一些其他功能。 配置网络虚拟设备时请注意以下因素：

* 可以使用服务终结点配置支持服务终结点的服务，这通常会出于成本或性能考虑因素而导致绕过 NVA。
* IP 地址依赖项适用于非 HTTP/S 流量（TCP 和 UDP 流量）。
* 可以在 NVA 设备中批准 FQDN HTTP/HTTPS 终结点。
* 将创建的路由表分配到 HDInsight 子网。

## <a name="service-endpoint-capable-dependencies"></a>支持服务终结点的依赖项

可以选择启用一个或多个以下服务终结点，这将导致绕过 NVA。 此选项可用于大量数据传输，以便节省成本和优化性能。 

| **终结点** |
|---|
| Azure SQL |
| Azure 存储 |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP 地址依赖项

| **终结点** | **详细信息** |
|---|---|
| [此处](hdinsight-management-ip-addresses.md)发布的 IP | 这些 IP 用于 HDInsight 控制位置，应包含在 UDR 中，以避免非对称路由 |
| AAD-DS 专用 IP | 仅 ESP 群集需要|


### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 依赖项

> [!Important]
> 以下列表仅提供了在创建群集后以及在群集操作的生存期期间，OS 和安全修补程序或证书验证可能需要的几个 FQDN。 可以获取 FQDN 依赖项（主要是 Azure 存储和 Azure 服务总线）的列表，用于[在此文件中](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)配置 NVA。 这些依赖项由 HDInsight 资源提供程序 (RP) 使用，以便成功创建和监视/管理群集。 其中包括遥测/诊断日志、预配元数据、群集相关配置、脚本、ARM 模板等。FQDN 依赖项列表可能会随未来 HDIngisht 更新的发布而更改。

| **终结点**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com:80                                                      |

## <a name="next-steps"></a>后续步骤

* [使用防火墙限制出站流量](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight 虚拟网络体系结构](hdinsight-virtual-network-architecture.md)
