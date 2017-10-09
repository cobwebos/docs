---
title: "在 Azure Active Directory 中设置 Cloud App Discovery | Microsoft Docs"
description: "提供有关如何使用 Cloud App Discovery 来查找和管理应用程序、这样做的好处以及其工作原理的信息。"
services: active-directory
keywords: "cloud app discovery, 管理应用程序"
documentationcenter: 
author: curtand
manager: femila
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: curtand
ms.reviewer: nigu
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: af54b77dc985f2ca6abeab29165278dfa598f5e2
ms.contentlocale: zh-cn
ms.lasthandoff: 09/26/2017

---

# <a name="set-up-cloud-app-discovery-in-azure-ad"></a>在 Azure AD 中设置 Cloud App Discovery

在 Azure AD 中，新的 Cloud App Discovery 增强功能现已随 Azure Active Directory Premium P1 许可证一同提供。 这些增强功能基于与 Microsoft Cloud App Security 的集成。 Cloud App Discovery 会比较超过 15,000 种云应用的 Cloud App Security 目录的流量日志，以便提供关于云使用和影子 IT 的实时信息。 

## <a name="prerequisites"></a>先决条件

组织必须具有 Azure AD Premium P1 许可证才能使用该产品。 有关详细信息，请参阅 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。

若要设置 Cloud App Discovery，你必须是 Azure Active Directory 的全局管理员或安全读者。 分配为管理员角色的用户在组织已订阅的所有云应用中具有相同的权限。

## <a name="setup-steps"></a>设置步骤

1. [设置快照报表](cloudappdiscovery-set-up-snapshots.md)，检查日志格式，确保日志向 Cloud App Discovery 提供可用信息。 快照报表还可提供对手动从防火墙和代理服务器上传的流量日志的临时可见性。

2. [设置连续报告](https://docs.microsoft.com/cloud-app-security/discovery-docker)，使用 Cloud App Security 日志收集器分析从网络转发的所有日志。 可以使用它们来标识新的应用和使用情况趋势。

3. 如果日志当前不受支持，请[设置自定义日志分析程序](https://docs.microsoft.com/en-us/cloud-app-security/custom-log-parser)，以便 Cloud App Discovery 可以对其进行分析。
  
## <a name="log-processing-flow"></a>日志处理流

它可能需要花费几分钟到几小时的时间来生成报表，具体取决于数据量。 下面是分析的具体内容：

* **上传** 来自网络的 Web 流量日志会上传到门户。
* **解析** Cloud App Security 会使用专用分析器对每个数据源进行解析并从流量日志提取流量数据。
* **分析** 流量数据会针对 Cloud App Security 目录进行分析以标识超过 15,000 个云应用。 还可以标识活动的用户和 IP 地址作为分析的一部分。
* **生成报表** Cloud App Security 会针对从日志文件提取的数据生成报表并将它提供给 Cloud App Discovery。

> [!NOTE]
> * 连续报表数据每天会分析两次。
> * 日志收集器会先压缩数据，然后再将其上传。 日志收集器的出站流量大约是所接收流量日志大小的 10%。

## <a name="using-traffic-logs-for-cloud-app-discovery"></a>将流量日志用于 Cloud App Discovery

Cloud App Discovery 会使用流量日志中的数据。 在日志中添加的详细信息越多，获得的可见性就越好。 Cloud App Discovery 需要具有以下属性的 Web 流量数据：

* 事务日期
* 源 IP 地址
* 源用户**建议**
* 目标 IP 地址
* 目标 URL **建议**（URL 相较于 IP 地址所提供的云应用检测的准确性更高）
* 总数据量
* 上传或下载的数据量，获得有关云应用使用情况模式的见解
* 采取的操作（允许/阻止的操作）

Cloud App Discovery 无法显示或分析不包含在日志中的属性。 例如，“Cisco ASA 防火墙”标准日志格式不包含“每个事务已上传的字节量”、“用户名”或“目标 URL”（但仅限目标 IP 地址）。 因此，通过此数据源可能对云应用具有较少的可见性。 对于 Cisco ASA 防火墙，请将信息级别设置为 6.1。

为了成功生成 Cloud App Discovery 报表，流量日志必须满足以下条件：

1.  数据源是[支持的防火墙或代理服务器](#supported-firewalls-and-proxies)。
2.  日志格式与预期标准格式匹配。 这将在上传时进行检查。 若要优化 uour 日志格式，请参阅[创建快照 Cloud App Discovery 报表](cloudappdiscovery-set-up-snapshots.md)。
3.  事件未超过 90 天。
4.  日志文件有效且包含出站流量信息。

## <a name="supported-firewalls-and-proxy-servers"></a>支持的防火墙和代理服务器

* Barracuda - Web 应用防火墙 (W3C)
* Blue Coat Proxy SG - 访问日志 (W3C)
* 检查点
* Cisco ASA 防火墙（对于 Cisco ASA 防火墙，请将信息级别设置为 6）
* Cisco IronPort WSA
* Cisco ScanSafe
* Cisco Meraki – URL 日志
* Clavister NGFW (Syslog)
* Dell Sonicwall
* Fortinet Fortigate
* Juniper SRX
* Juniper SSG
* McAfee Secure Web Gateway
* Microsoft Forefront Threat Management Gateway (W3C)
* Palo Alto 系列防火墙
* Sophos SG
* Sophos Cyberoam
* Squid（通用）
* Squid（本机）
* Websense - Web 安全解决方案 - 调查详细报表 (CSV)
* Websense - Web 安全解决方案 - Internet 活动日志 (CEF)
* Zscaler

> [!NOTE]
> Cloud App Discovery 支持 IPv4 和 IPv6 地址。

如果你的日志不受支持，请选择“其他”作为“数据源”，并指定尝试上传的设备和日志。 该日志将由 Cloud App Security 云分析人员团队审核。 我们会在添加了日志类型支持时通知你，不过你可以定义与日志格式匹配的自定义分析器。 有关详细信息，请参阅[使用自定义日志分析器](https://docs.microsoft.com/cloud-app-security/custom-log-parser)。

## <a name="data-attributes-according-to-vendor-documentation"></a>数据属性（根据供应商文档）

| 数据源         | 目标应用 URL | 目标应用 IP 地址 | 用户名 | 源 IP 地址 | 总流量 | 已上传的字节 |
|-----------------------------------------|----------------|---------------|----------|-----------|---------------|----------------|
| Barracuda                               | **是**        | **是**       | **是**  | **是**   | 否            | 否             |
| Blue Coat                               | **是**        | 否            | **是**  | **是**   | **是**       | **是**        |
| Checkpoint                              | 否             | **是**       | 否       | **是**   | 否            | 否             |
| Cisco ASA                               | 否             | **是**       | 否       | **是**   | **是**       | 否             |
| Cisco FWSM                              | 否             | **是**       | 否       | **是**   | **是**       | 否             |
| Cisco Ironport WSA                      | **是**        | **是**       | **是**  | **是**   | **是**       | **是**        |
| Cisco Meraki                            | **是**        | **是**       | 否       | **是**   | 否            | 否             |
| Clavister NGFW (Syslog)                 | **是**        | **是**       | **是**  | **是**   | **是**       | **是**        |
| Dell SonicWall                          | **是**        | **是**       | 否       | **是**   | **是**       | **是**        |
| Fortigate                               | 否             | **是**       | 否       | **是**   | **是**       | **是**        |
| Juniper SRX                             | 否             | **是**       | 否       | **是**   | **是**       | **是**        |
| Juniper SSG                             | 否             | **是**       | 否       | **是**   | **是**       | **是**        |
| McAfee SWG                              | **是**        | 否            | 否       | **是**   | **是**       | **是**        |
| MS TMG                                  | **是**        | 否            | **是**  | **是**   | **是**       | **是**        |
| Palo Alto Networks                      | **是**        | **是**       | **是**  | **是**   | **是**       | **是**        |
| Sophos                                  | **是**        | **是**       | **是**  | **是**   | **是**       | 否             |
| Squid（通用）                          | **是**        | 否            | **是**  | **是**   | 否            | **是**        |
| Squid（本机）                          | **是**        | 否            | **是**  | **是**   | 否            | **是**        |
| Websense - 调查报表 (CSV)   | **是**        | **是**       | **是**  | **是**   | **是**       | **是**        |
| Websense - Internet 活动日志 (CEF)  | **是**        | **是**       | **是**  | **是**   | **是**       | **是**        |
| Zscaler                                 | **是**        | **是**       | **是**  | **是**   | **是**       | **是**        |


## <a name="next-steps"></a>后续步骤
使用以下链接继续在 Azure AD 中设置 Cloud App Discovery。

* [创建快照报表](cloudappdiscovery-set-up-snapshots.md)
* [配置持续报告](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [使用自定义日志分析器](https://docs.microsoft.comcommit/cloud-app-security/custom-log-parser)
