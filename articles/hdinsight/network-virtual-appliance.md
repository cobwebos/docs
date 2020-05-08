---
title: 在 Azure HDInsight 中配置网络虚拟设备
description: 了解如何在 Azure HDInsight 中为网络虚拟设备配置一些其他功能。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: cbc2104ae3c55ae3670867b7a253d812f3a4be0e
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864703"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>在 Azure HDInsight 中配置网络虚拟设备

> [!Important]
> **仅当**所要配置的网络虚拟设备 (NVA) 不是 Azure 防火墙时，才需要以下信息。

对于许多常见的重要方案，Azure 防火墙已自动配置为允许流量。 使用另一个网络虚拟设备将需要配置多个附加功能。 配置网络虚拟设备时，请注意以下因素：

* 应在支持服务终结点的服务中配置服务终结点。
* IP 地址依赖项适用于非 HTTP/S 流量（TCP 和 UDP 流量）。
* 可将 FQDN HTTP/HTTPS 终结点放在 NVA 设备中。
* 通配符 HTTP/HTTPS 终结点是可以根据许多限定符变化的依赖项。
* 将创建的路由表分配到 HDInsight 子网。

## <a name="service-endpoint-capable-dependencies"></a>支持服务终结点的依赖项

| **终结点** |
|---|
| Azure SQL |
| Azure 存储 |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP 地址依赖项

| **终结点** | **详细信息** |
|---|---|
| \*:123 | NTP 时钟检查。 在端口 123 上的多个终结点中检查流量 |
| [此处](hdinsight-management-ip-addresses.md)发布的 IP | 这些 Ip 是 HDInsight 服务 |
| ESP 群集的 AAD-DS 专用 IP |
| \*:16800，用于 KMS Windows 激活 |
| \*12000，用于 Log Analytics |

### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 依赖项

> [!Important]
> 以下列表仅提供了一些最重要的 FQDN。 你可以[在此文件中](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)获取其他 fqdn （主要是 Azure 存储和 Azure 服务总线）来配置你的 NVA。

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
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>后续步骤

* [使用防火墙限制出站流量](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight 虚拟网络体系结构](hdinsight-virtual-network-architecture.md)
