---
title: Azure Sentinel 数据规范化架构参考 |Microsoft Docs
description: 本文显示 Azure Sentinel 数据规范化架构。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: eb1752ea66f2cbebf6a653705b5a760e8e268240
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933439"
---
# <a name="azure-sentinel-data-normalization-schema-reference"></a>Azure Sentinel 数据规范化架构引用

## <a name="terminology"></a>术语

Sentinel 的架构中使用了以下术语：

| 术语 | 定义 |
| ---- | ---------- |
| 报表设备 | 向 Azure Sentinel 发送记录的系统。 它可能不是记录的主题系统。 |
| 记录 | 从报告设备发送的数据单位。 这通常称为 "日志"、"事件" 或 "警报"，但不一定必须是其中之一。 |
|

## <a name="data-types-and-formats"></a>数据类型和格式

应根据下面的准则对值进行规范化。 这对于规范化字段是必需的，建议用于其他字段。 

| 数据类型 | 物理类型 | 格式和值 |
| --------- | ------------- | ---------------- |
| **日期/时间** | 根据引入方法，使用降序优先级：<ul><li>Log Analytics 内置日期时间类型</li><li>使用 Log Analytics 日期时间数值表示形式的整数字段</li><li>使用 Log Analytics 日期时间数值表示形式的字符串字段</li></ul> | Log Analytics 日期时间表示形式。 <br></br>Log Analytics 日期 & 时间表示形式在本质上类似，但不同于 Unix 时间表示形式。 请参阅这些转换指导原则。 <br></br>日期 & 时间应为时区进行调整。 |
| **MAC 地址** | 字符串 | Colon-Hexadecimal 表示法 |
| **IP 地址** | IP 地址 | 架构没有单独的 IPv4 和 IPv6 地址。 "任何 IP 地址" 字段可能包含 IPv4 地址或 IPv6 地址：<ul><li>十进制表示法中的 IPv4</li><li>IPv6 采用 8 hextets 表示法，允许在此处介绍的简短形式。</li></ul> |
| **User** | 字符串 | 以下3个用户字段可用：<ul><li>用户名</li><li>用户 UPN</li><li>用户域</li></ul> |
| **用户 ID** | 字符串 | 目前支持以下2个用户 Id：<ul><li>用户 SID</li><li>Azure Active directory ID</li></ul> |
| **设备** | 字符串 | 支持以下3个设备/主机列：<ul><li>ID</li><li>名称</li><li>完全限定的域名 (FQDN)</li></ul> |
| **国家/地区** | 字符串 | 按照此优先级使用 ISO 3166-1 的字符串：<ul><li>Alpha-2 代码 (即美国的) </li><li> (，例如 USA 美国的3个字母的代码) </li><li>短名称</li></ul> |
| **区域** | 字符串 | 使用 ISO 3166-2 的国家/地区子分区名称 |
| **城市** | 字符串 | |
| **精度** | Double | ISO 6709 符号的坐标表示形式 (带符号的十进制)  |
| **纬度** | Double | ISO 6709 符号的坐标表示形式 (带符号的十进制)  |
| **哈希算法** | 字符串 | 支持以下4个哈希列：<ul><li>MD5</li><li>SHA1</li><li>SHA256</li><li>SHA512</li></ul> |
| **文件类型** | 字符串 | 文件类型的类型：<ul><li>分机</li><li>类</li><li>NamedType</li></ul> |
| 

## <a name="network-sessions-table-schema"></a>网络会话表架构

下面是 "网络会话" 表的架构，版本1.0。0

| 字段名称 | 值类型 | 示例 | 说明 | 关联的 OSSEM 实体 |
|-|-|-|-|-|
| EventType | 字符串 | 交通 | 正在收集的事件的类型 | 事件 |
| EventSubType | 字符串 | 身份验证 | 类型的其他说明（如果适用） | 事件 |
| EventCount | Integer  | 10 | 聚合的事件数（如果适用）。 | 事件 |
| EventEndTime | 日期/时间 | 请参阅 "数据类型" | 事件的结束时间 | 事件 |
| EventMessage | 字符串 |  访问被拒绝 | 一般消息或说明，包括在记录中或从记录生成 | 事件 |
| DvcIpAddr | IP 地址 |  23.21.23.34 | 生成记录的设备的 IP 地址 | 设备，<br>IP |
| DvcMacAddr | 字符串 | 06：10：9f： eb：8f：14 | 从中发送事件的报表设备的网络接口的 MAC 地址。 | 设备，<br>Mac |
| DvcHostname |  (字符串) 的设备名称 | syslogserver1.contoso.com | 生成消息的设备的设备名称。 | 设备 |
| EventProduct | 字符串 | OfficeSharepoint | 生成事件的产品。 | 事件 |
| EventProductVersion | 字符串 | 9.0 |  生成事件的产品的版本。 | 事件 |
| EventResourceId |  (字符串) 的设备 ID | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | 生成消息的设备的资源 ID。 | 事件 |
| EventReportUrl | 字符串 | https://192.168.1.1/repoerts/ae3-56.htm | 报表设备创建的完整报表的链接 | 事件 |
| EventVendor | 字符串 | Microsoft | 生成事件的产品的供应商。 | 事件 |
| EventResult | 多值：成功、部分、失败、[Empty] (字符串)  | 成功 | 为活动报告的结果。 如果不适用，则为空值。 | 事件 |
| EventResultDetails | 字符串 | 密码错误 | EventResult 中报告的结果的原因或详细信息 | 事件 |
| EventSchemaVersion | Real | 0.1 | Azure Sentinel 架构版本。 目前为0.1。 | 事件 |
| EventSeverity | 字符串 | 低 | 如果报告的活动具有安全影响，则表示影响的严重性。 | 事件 |
| EventOriginalUid | 字符串 | af6ae8fe-ff43-4a4c-b537-8635976a2b51 | 来自报表设备的记录 ID。 | 事件 |
| EventStartTime | 日期/时间 | 请参阅 "数据类型" | 事件的指定时间 | 事件 |
| TimeGenerated | 日期/时间 | 请参阅 "数据类型" | 事件发生的时间，按报告源的报告。 | 自定义字段 |
| EventTimeIngested | 日期/时间 | 请参阅 "数据类型" | 事件引入 Azure Sentinel 的时间。 将由 Azure Sentinel 添加。 | 事件 |
| EventUid | Guid (字符串)  | 516a64e3-8360-4f1e-a67c-d96b3d52df54 | Sentinel 用于标记行的唯一标识符。 | 事件 |
| NetworkApplicationProtocol | 字符串 | HTTPS | 连接或会话使用的应用程序层协议。 | 网络 |
| DstBytes | int | 32455 | 从目标到连接或会话的源发送的字节数。 | 目标 |
| SrcBytes | int | 46536 | 从源发送到连接或会话的字节数。 | 源 |
| NetworkBytes | int | 78991 | 双向发送的字节数。 如果 BytesReceived 和内 bytessent 同时存在，则 BytesTotal 应等于它们的总和。 | 网络 |
| NetworkDirection | 多值：入站、出站 (字符串)  | 入站 | 连接或会话进出组织的方向。 | 网络 |
| DstGeoCity | 字符串 | 伯灵顿 | 与目标 IP 地址关联的城市 | 位置<br>地域 |
| DstGeoCountry | 国家 (字符串)  | USA | 与源 IP 地址关联的国家/地区 | 位置<br>地域 |
| DstDvcHostname |  (字符串) 的设备名称 |  victim_pc | 目标设备的设备名称 | 目标<br>设备 |
| DstDvcFqdn | 字符串 | victim_pc。 | 在其中创建了日志的主机的完全限定的域名 | 位置<br>设备 |
| DstDomainHostname | 字符串 | CONTOSO | 目标的域、目标主机的域 (网站、域名等 ) ，例如 DNS 查找或 NS 查找 | 目标 |
| DstInterfaceName | 字符串 | Microsoft Hyper-V 网络适配器 | 目标设备用于连接或会话的网络接口。 | 目标 |
| DstInterfaceGuid | 字符串 | 2BB33827-6BB6-48DB-8DE6-DB9E0B9F9C9B | 用于身份验证请求的网络接口的 GUID  | 目标 |
| DstIpAddr | IP 地址 | 2001： db8：： ff00：42：8329 | 连接或会话目标的 IP 地址，最常称为网络数据包中的目标 IP | 位置<br>IP |
| DstDvcIpAddr | IP 地址 | 75.22.12.2 | 不与网络数据包直接关联的设备的目标 IP 地址 | 位置<br>设备，<br>IP
| DstGeoLatitude | 纬度 (双)  | 44.475833 | 与目标 IP 地址关联的地理坐标的纬度 | 位置<br>地域 |
| DstMacAddr | 字符串 | 06：10：9f： eb：8f：14 | 连接或会话已终止的网络接口的 MAC 地址，最常引用网络数据包中的目标 MAC | 位置<br>MAC |
| DstDvcMacAddr | 字符串 | 06：10：9f： eb：8f：14 | 不与网络数据包直接关联的设备的目标 MAC 地址。 | 位置<br>设备，<br>MAC |
| DstDvcDomain | 字符串 | CONTOSO | 目标设备的域。 | 位置<br>设备 |
| DstPortNumber | 整数 | 443 | 目标 IP 端口。 | 位置<br>端口 |
| DstGeoRegion | 区域 (字符串)  | 佛蒙特州 | 与目标 IP 地址关联的国家/地区内的区域 | 位置<br>地域 |
| DstResourceId |  (字符串) 的设备 ID |  /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /victim | 目标设备的资源 Id。 | 目标 |
| DstNatIpAddr | IP 地址 | 2::1 | 如果中介 NAT 设备（如防火墙）报告，则 NAT 设备使用的 IP 地址与源通信。 | 目标 NAT，<br>IP |
| DstNatPortNumber | int | 443 | 如果中介 NAT 设备（如防火墙）报告，则 NAT 设备使用该端口与源通信。 | 目标 NAT，<br>端口 |
| DstUserSid | 用户 SID |  S-12-1445 | 与会话的目标关联的标识的用户 ID。 通常，用于对服务器进行身份验证的标识。 有关详细信息，请参阅 "数据类型"。 | 位置<br>用户 |
| DstUserAadId | 字符串 (guid)  | ae92b0b4-cfba-4b42-85a0-fbd862f4df54 | 会话目标端用户的 Azure AD 帐户对象 ID | 位置<br>用户 |
| DstUserName | 用户名 (字符串)  | johnd | 与会话的目标关联的标识的用户名。  | 位置<br>用户 |
| DstUserUpn | 字符串 | johnd@anon.com | 与会话的目标关联的标识的 UPN。 | 位置<br>用户 |
| DstUserDomain | 字符串 | WORKGROUP | 会话目标中帐户的域名或计算机名 | 位置<br>用户 |
| DstZone | 字符串 | 外围网络 | 报表设备定义的目标网络区域。 | 目标 |
| DstGeoLongitude | 经度 (双)  | -73.211944 | 与目标 IP 地址关联的地理坐标的经度 | 位置<br>地域 |
| DvcAction | 多值： Allow、Deny、Drop (string)  | Allow | 如果某个中介设备（如防火墙）报告，则设备执行的操作。 | 设备 |
| DvcInboundInterface | 字符串 | eth0 | 如果某个中介设备（如防火墙）报告，则该设备使用的网络接口来连接到源设备。 | 设备 |
| DvcOutboundInterface | 字符串  | 以太网适配器以太网4 | 如果某个中介设备（如防火墙）报告，则该设备使用的网络接口与目标设备的连接。 | 设备 |
| NetworkDuration | 整数 | 1500 | 完成网络会话或连接的时间长度（以毫秒为单位） | 网络 |
| NetworkIcmpCode | 整数 | 34 | 对于 ICMP 消息，ICMP 消息类型数值 (RFC 2780 或 RFC 4443) 。 | 网络 |
| NetworkIcmpType | 字符串 | 目标不可访问 | 对于 ICMP 消息， (RFC 2780 或 RFC 4443) 的 ICMP 消息类型文本表示形式。 | 网络 |
| DstPackets | int  | 446 | 从目标发送到连接或会话源的数据包数。 数据包的含义由报表设备定义。 | 目标 |
| SrcPackets | int  | 6478 | 从源发送到连接或会话目标的数据包数。 数据包的含义由报表设备定义。 | 源 |
| NetworkPackets | int  | 0 | 双向发送的数据包数。 如果 PacketsReceived 和 PacketsSent 同时存在，则 BytesTotal 应等于它们的总和。 | 网络 |
| HttpRequestTime | 整数 | 700 | 将请求发送到服务器所花费的时间（如果适用）。 | Http |
| HttpResponseTime | 整数 | 800 | 在服务器中接收响应所花费的时间（如果适用）。 | Http |
| NetworkRuleName | 字符串 | AnyAnyDrop | 确定 DeviceAction 时所依据的规则的名称或 ID | 网络 |
| NetworkRuleNumber | int |  23 | 匹配的规则号  | 网络 |
| NetworkSessionId | 字符串 | 172_12_53_32_4322__123_64_207_1_80 | 报告设备报告的会话标识符。 例如，以下身份验证后的特定应用程序的 L7 会话标识符 | 网络 |
| SrcGeoCity | 字符串 | 伯灵顿 | 与源 IP 地址关联的城市 | 源程序<br>地域 |
| SrcGeoCountry | 国家 (字符串)  | USA | 与源 IP 地址关联的国家/地区 | 源程序<br>地域 |
| SrcDvcHostname |  (字符串) 的设备名称 |  villain | 源设备的设备名称 | 源程序<br>设备 |
| SrcDvcFqdn | 字符串 | Villain.malicious.com | 在其中创建了日志的主机的完全限定的域名 | 源程序<br>设备 |
| SrcDvcDomain | 字符串 | EVILORG | 从中启动会话的设备的域 | 源程序<br>设备 |
| SrcDvcOs | 字符串 | iOS | 源设备的操作系统 | 源程序<br>设备 |
| SrcDvcModelName | 字符串 | Samsung Galaxy 便笺 | 源设备的型号名称 | 源程序<br>设备 |
| SrcDvcModelNumber | 字符串 | 10.0 | 源设备的型号 | 源程序<br>设备 |
| SrcDvcType | 字符串 | 移动型 | 源设备的类型 | 源程序<br> 设备 |
| SrcIntefaceName | 字符串 | eth01 | 源设备用于连接或会话的网络接口。 | 源 |
| SrcInterfaceGuid | 字符串 | 46ad544b-eaf0-47ef-827c-266030f545a6 | 使用的网络接口的 GUID | 源 |
| SrcIpAddr | IP 地址 | 77.138.103.108 | 从中发起连接或会话的 IP 地址。 | 源程序<br>IP |
| SrcDvcIpAddr | IP 地址 | 77.138.103.108 | 不与网络数据包直接关联的设备的源 IP 地址 (由提供程序收集的或显式计算的) 。 | 源程序<br>设备，<br>IP |
| SrcGeoLatitude | 纬度 (双)  | 44.475833 | 与源 IP 地址关联的地理坐标的纬度 | 源程序<br>地域 |
| SrcGeoLongitude | 经度 (双)  | -73.211944 | 与源 IP 地址关联的地理坐标的经度 | 源程序<br>地域 |
| SrcMacAddr | 字符串 | 06：10：9f： eb：8f：14 | 发出连接 od 会话的网络接口的 MAC 地址。 | 源程序<br>Mac |
| SrcDvcMacAddr | 字符串 | 06：10：9f： eb：8f：14 | 不与网络数据包直接关联的设备的源 MAC 地址。 | 源程序<br>设备，<br>Mac |
| SrcPortNumber | 整数 | 2335 | 发起连接的 IP 端口。 可能与包含多个连接的会话无关。 | 源程序<br>端口 |
| SrcGeoRegion | 区域 (字符串)  | 佛蒙特州 | 与源 IP 地址关联的国家/地区内的区域 | 源程序<br>地域 |
| SrcResourceId | 字符串 | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | 生成消息的设备的资源 ID。 | 源 |
| SrcNatIpAddr | IP 地址 | 4.3.2.1 | 如果中介 NAT 设备（如防火墙）报告，则 NAT 设备用于与目标通信的 IP 地址。 | 源 NAT，<br>IP |
| SrcNatPortNumber | 整数 | 345 | 如果由中间 NAT 设备（如防火墙）报告，则 NAT 设备用于与目标通信的端口。 | 源 NAT，<br>端口 |
| SrcUserSid |  (字符串的用户 ID)  | S-15-1445 | 与会话源关联的标识的用户 ID。 通常，用户在客户端上执行操作。 有关详细信息，请参阅 "数据类型"。 | 源程序<br>用户 |
| SrcUserAadId | 字符串 (guid)  | 16c8752c-7dd2-4cad-9e03-fb5d1cee5477 | 会话源端用户的 Azure AD 帐户对象 ID | 源程序<br>用户 |
| SrcUserName | 用户名 (字符串)  | 相距 | 与会话源关联的标识的用户名。 通常，用户在客户端上执行操作。 有关详细信息，请参阅 "数据类型"。 | 源<br>用户 |
| SrcUserUpn | 字符串 | bob@alice.com | 启动会话的帐户的 UPN | 源程序<br>用户 |
| SrcUserDomain | 字符串 | 桌面 | 发起会话的帐户的域 | 源程序<br>用户 |
| SrcZone | 字符串 | 点击 | 由报表设备定义的源的网络区域。 | 源 |
| NetworkProtocol | 字符串 | TCP | 连接或会话使用的 IP 协议。 通常为 TCP、UDP 或 ICMP | 网络 |
| CloudAppName | 字符串 | Facebook | 由代理标识的 HTTP 应用程序的目标应用程序的名称。 | 云 |
| CloudAppId | 字符串 | 124 | 代理标识的 HTTP 应用程序的目标应用程序 ID。 此值通常特定于所使用的代理。 | 云 |
| CloudAppOperation | 字符串 | DeleteFile | 用户在用于 HTTP 应用程序的目标应用程序的上下文中执行的操作，由代理标识。 此值通常特定于所使用的代理。 | 云 |
| CloudAppRiskLevel | 字符串 | 3 | 与代理标识的 HTTP 应用程序相关联的风险级别。 此值通常特定于所使用的代理。 | 云 |
| FileName | 字符串 | ImNotMalicious.exe | 通过网络连接传输的文件名，用于提供文件名信息的协议（如 FTP 和 HTTP）。 | 文件 |
| 文件路径 | 字符串 | C:\Malicious\ImNotMalicious.exe | 文件的完整路径，包括文件名 | 文件 |
| FileHashMd5 | 字符串 | 51BC68715FC7C109DCEA406B42D9D78F | 通过网络连接传输的协议的 MD5 哈希值。 | 文件 |
| FileHashSha1 | 字符串 | 491AE3...C299821476F4 | 通过网络连接协议传输的文件的 SHA1 哈希值。 | 文件 |
| FileHashSha256 | 字符串 | 9B8F8EDB...C129976F03 | 通过网络连接传输的协议的 SHA256 哈希值。 | 文件 |
| FileHashSha512 | 字符串 | 5E127D...F69F73F01F361 | 通过网络连接协议传输的文件的 SHA512 哈希值。 | 文件 |
| FileExtension |  字符串 | exe | 通过网络连接传输的、用于协议（如 FTP 和 HTTP）的文件的类型。 | 文件
| FileMimeType | 字符串 | application/msword | 通过网络连接传输的文件的 MIME 类型（如 FTP 和 HTTP） | 文件 |
| FileSize | 整数 | 23500 | 通过网络连接传输的协议文件的文件大小（以字节为单位）。 | 文件 |
| HttpVersion | 字符串 | 2.0 | HTTP/HTTPS 网络连接的 HTTP 请求版本。 | Http |
| HttpRequestMethod | 字符串 | GET | Http/HTTPS 网络会话的 HTTP 方法。 | Http |
| HttpStatusCode | 字符串 | 404 | HTTP/HTTPS 网络会话的 HTTP 状态代码。 | Http |
| HttpContentType | 字符串 | 多部分/窗体数据;边界 = 内容 | HTTP/HTTPS 网络会话的 HTTP 响应内容类型标头。 | Http |
| HttpReferrerOriginal | 字符串 | https://developer.mozilla.org/en-US/docs/Web/JavaScript | HTTP/HTTPS 网络会话的 HTTP 引用标头。 | Http |
| HttpUserAgentOriginal | 字符串 | Mozilla/5.0 (Windows NT 10.0;WOW64) AppleWebKit/537.36 (KHTML，如 Gecko) Chrome/83.0.4103.97 Safari/537.36 | Http/HTTPS 网络会话的 HTTP 用户代理标头。 | Http |
| HttpRequestXff | 字符串 | 120.12.41.1 | Http/HTTPS 网络会话的 HTTP X 转发的标头。 | Http |
| UrlCategory | 字符串 | 搜索引擎 | 已定义的 URL 分组 (或可能只基于 URL 中的域) 与 (的内容相关，例如：成年人、新闻、广告、寄存域等 )  | url |
| UrlOriginal | 字符串 | https://contoso.com/fo/?k=v&q = u # f | HTTP/HTTPS 网络会话的 HTTP 请求 URL。 | Url |
| UrlHostname | 字符串 | contoso.com | Http/HTTPS 网络会话的 HTTP 请求 URL 的域部分。 | Url |
| ThreatCategory | 字符串 | 特洛伊 | 安全系统所标识的威胁的类别，例如 IP 的 Web 安全网关，并与此网络会话关联。 | 威胁 |
| ThreatId | 字符串 | Tr. 124 | 安全系统识别的威胁的 ID，例如 IP 的 Web 安全网关，并与此网络会话关联。 | 威胁 |
| ThreatName | 字符串 | EICAR 测试文件 | 识别的威胁或恶意软件的名称 | 威胁 |
| AdditionalFields | 动态 (JSON 包)  | {<br>Property1： "val1"，<br>Property2： "val2"<br>} | 如果架构中没有相应的列匹配，则可将其他字段存储在 JSON 包中。<br>在查询时进行分析时，建议不要使用此方法，因为 JSON 数据会降低查询性能。 相反，建议提升其他列。<br>对于未来的引入时间分析方案，还会将其他数据收集到此 JSON 包列中。 | 自定义字段 |
| 
