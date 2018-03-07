---
title: "Azure Log Analytics 中的网络性能监视器解决方案 | Microsoft 文档"
description: "使用网络性能监视器中的服务终结点管理器功能，可以监视与带有开放 TCP 端口的任何终结点之间建立的网络连接。"
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: ba19a4fc24668bff27c961b5b415f840d1132a34
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="service-endpoint-monitor"></a>服务终结点监视器

使用[网络性能监视器](log-analytics-network-performance-monitor.md)中的服务终结点管理器功能，可以监视与带有开放 TCP 端口的任何终结点之间建立的网络连接。 此类终结点包括网站、SaaS 应用程序、PaaS 应用程序和 SQL 数据库。 

使用**服务终结点监视器**可执行以下功能： 

- 从多个分支机构/位置监视与应用程序和网络服务（例如 Office 365、Dynamics CRM、内部业务线应用程序、SQL 数据库等）建立的网络连接 
- 内置测试，以便监视与 Office365 和 Dynamics365 终结点建立的网络连接 
- 确定在连接到终结点时出现的响应时间、网络延迟和数据包丢失情况 
- 确定应用程序的性能是由于网络问题还是应用程序提供商一端出现某种问题而变差 
- 通过查看拓扑图中每个跃点造成的延迟，来识别网络中可能导致应用程序性能变差的热点。 


![服务终结点监视器](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>配置 
若要打开网络性能监视器的配置，请打开[网络性能监视器解决方案](log-analytics-network-performance-monitor.md)并单击“配置”按钮。

![配置网络性能监视器](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-oms-agents-for-the-monitoring"></a>配置 OMS 代理用于监视。  
在用于监视的节点上启用以下防火墙规则，以便解决方案能够发现从节点到服务终结点的拓扑： 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action 
```

### <a name="create-service-endpoint-monitor-tests"></a>创建服务终结点监视器测试 

开始创建测试，以便监视与服务终结点建立的网络连接 

1. 单击“服务终结点监视器”选项卡。
2. 单击“添加测试”并输入测试名称和说明。 
3. 选择测试类型：<br>如果要监视响应 HTTP/S 请求的服务（例如 outlook.office365.com、bing.com）的连接，请选择“Web 测试”。<br>如果要监视响应 TCP 请求，但不响应 HTTP/S 请求的服务（例如 SQL 服务器、FTP 服务器、SSH 端口等）的连接，请选择“网络测试”。 
4. 如果不希望执行网络度量（网络延迟、数据包丢失、拓扑发现），请取消选中该复选框。 我们建议保留选中此项，以便最大程度地利用此功能。 
5. 输入要监视其网络连接的目标 URL/FQDN/IP 地址。  
6. 输入目标服务的端口号。 
7. 输入运行测试的频率。 
8. 选择要从中监视服务网络连接的节点。 

    >[!NOTE]
    > 对于基于 Windows 服务器的节点，该功能使用基于 TCP 的请求来执行网络度量。 对于基于 Windows 客户端的节点，该功能使用基于 ICMP 的请求来执行网络度量。 在某些情况下，由于节点基于 Windows 客户端，目标应用程序会阻止基于 ICMP 的传入请求，在此情况下，解决方案无法执行网络度量。 因此，我们建议在这种情况下使用基于 Windows 服务器的节点。 

9. 如果不希望针对所选项生成运行状况事件，请清除“启用对此测试覆盖的目标进行运行状况监视”。 
10. 选择监视条件。 可以通过键入阈值，针对运行状况事件生成设置自定义阈值。 只要条件值超出其针对所选网络/子网对选择的阈值，就会生成运行状况事件。 
11. 单击“保存”以保存配置。 

 ![服务终结点监视器配置](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>演练 

转到网络性能监视仪表板视图，在“服务终结点监视器”页中查看所创建的不同测试的运行状况摘要。  

![服务终结点监视器页](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

在“测试”页上单击磁贴向下钻取并查看测试的详细信息。 在 LHS 表中，可以查看所有测试的时间点运行状况，以及服务响应时间、网络延迟和数据包丢失值。 可以使用“网络状态记录器”控件查看过去另一时间的网络快照。 在要调查的表中单击“测试”。 可以通过 RHS 窗格中的图表查看丢包、延迟和响应时间值的历史趋势。 单击“测试详细信息”链接查看每个节点的性能。 

![服务终结点监视器测试](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

在“测试节点”视图中，可以观察每个节点的网络连接。 单击性能下降的节点。  这是在应用程序中观察到运行速度缓慢的节点。 

观察应用程序响应时间与网络延迟之间的相关性，确定应用程序性能变差的原因是网络问题，还是应用程序提供商一端出现某种问题。 

**应用程序问题：**如果响应时间出现峰值，但网络延迟保持一致，则表示网络正常，问题出现在应用程序端。  

![服务终结点监视器应用程序问题](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

**网络问题：**如果出现响应时间峰值的同时，网络延迟也出现了相应的峰值，则表示响应时间增大的原因是网络延迟增大。  

![服务终结点监视器网络问题](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

确定问题是网络原因后，可以在拓扑图上单击“拓扑”视图链接，来识别有问题的跃点。 例如，在下图中可以看到，节点与应用程序终结点之间的总延迟为 105 毫秒，其中，96 毫秒的延迟是带有红色标记的跃点造成的。 识别有问题的跃点后，可以采取纠正措施。  

![服务终结点监视器测试](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>诊断 

如果观察到异常情况，请执行以下步骤：

如果服务响应时间、网络断开和延迟显示为 NA，则问题可能是下面一个或多个原因造成的：
- 应用程序已关闭。
- 用于检查服务网络连接的节点已关闭。
- 在测试配置中输入的目标不正确。
- 节点未建立任何网络连接。

如果显示了有效的服务响应时间，但网络断开和延迟显示为 NA，则问题可能是下面一个或多个原因造成的：
- 如果用于检查服务网络连接的节点是 Windows 客户端计算机，则原因是目标服务正在阻止 ICMP 请求，或者网络防火墙正在阻止 ICMP 来自该节点的请求。
- 在测试配置中已取消选中“执行网络度量”复选框。 

如果服务响应时间为 NA 但网络断开和延迟值有效，则表示目标服务不是 Web 应用程序。 编辑测试配置，选择“网络测试”而不是“Web 测试”作为测试类型。 

如果应用程序运行速度缓慢，应确定应用程序的性能是由于网络问题还是应用程序提供商一端出现某种问题而变差。


## <a name="next-steps"></a>后续步骤
* [搜索日志](log-analytics-log-searches.md)以查看详细的网络性能数据记录。
