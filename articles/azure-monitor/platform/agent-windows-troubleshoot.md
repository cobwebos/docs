---
title: 排查 Windows Log Analytics 代理问题
description: 介绍 Azure Monitor 中适用于 Windows 的 Log Analytics 代理最常见问题的症状、原因和解决方法。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 06/12/2019
ms.openlocfilehash: 7bf0c8429eaecd6cba83872cbea5876cc0c31221
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199007"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>如何排查适用于 Windows 的 Log Analytics 代理问题 

本文提供了有关在 Azure Monitor 中为 Windows Log Analytics 代理带来的问题的疑难解答，并建议解决这些问题的可能解决方案。

如果这些步骤对你均无效，我们还提供了以下支持渠道：

* 具有顶级支持权益的客户可以通过[顶级支持](https://premier.microsoft.com/)提出支持请求。
* 签订了 Azure 支持协议的客户可以在 [Azure 门户](https://manage.windowsazure.com/?getsupport=true)中提出支持请求。
* 请访问 Log Analytics 反馈页面，查看已提交的想法和 bug [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback)或提交新的想法或 bug。 

## <a name="important-troubleshooting-sources"></a>重要故障排除源

 为了帮助解决与 Windows Log Analytics 代理相关的问题，代理将事件记录到 Windows 事件日志中，特别是在*Application and Services\Operations Manager*下。  

## <a name="connectivity-issues"></a>连接性问题

如果代理通过代理服务器或防火墙进行通信，则存在阻止源计算机与 Azure Monitor 服务的通信的就地限制。 如果通信被阻止，由于配置错误，在尝试安装代理时，与工作区的注册可能会失败，或将代理安装后配置为向其他工作区报告。 注册成功后，代理通信可能会失败。 本部分介绍了在 Windows 代理中对此类问题进行故障排除的方法。

仔细检查防火墙或代理是否已配置为允许下表中所述的以下端口和 Url。 同时确认未对 web 流量启用 HTTP 检查，因为这会阻止代理和 Azure Monitor 之间的安全 TLS 通道。  

|代理资源|端口 |Direction |绕过 HTTPS 检查|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |端口 443 |出站|是 |  
|*.oms.opinsights.azure.com |端口 443 |出站|是 |  
|\* .blob.core.windows.net |端口 443 |出站|是 |  
|*.azure-automation.net |端口 443 |出站|是 |  

有关 Azure 政府版所需的防火墙信息，请参阅[Azure 政府版管理](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)。 

可以通过多种方式来验证代理是否成功与 Azure Monitor 通信。

- 在工作区中启用[Azure Log Analytics 代理运行状况评估](../insights/solution-agenthealth.md)。 从 "代理运行状况" 仪表板中，查看**无响应代理**列的计数，迅速查看是否列出了代理。  

- 运行以下查询，确认代理正在将检测信号发送到它配置为向其报告的工作区。 将 `<ComputerName>` 替换为计算机的实际名称。

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    如果计算机与服务成功通信，则查询应返回结果。 如果查询未返回结果，请首先验证是否已将代理配置为向正确的工作区报告。 如果配置正确，请继续执行步骤3，搜索 Windows 事件日志，以确定代理是否记录了可能阻止其与 Azure Monitor 通信的问题。

- 确定连接问题的另一种方法是运行**TestCloudConnectivity**工具。 默认情况下，该工具与 *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent*文件夹中的代理一起安装。 在提升的命令提示符下，导航到文件夹并运行该工具。 此工具将返回结果并突出显示测试失败的位置（例如，如果它与已阻止的特定端口/URL 相关）。 

    ![TestCloudConnection 工具执行结果](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- 按**事件源**筛选*Operations Manager*事件日志  - *运行状况服务模块*、*运行状况服务*和*服务连接器*，并按**事件级别**的*警告*和*错误*筛选确认它是否已从下表中写入事件。 如果是，请查看每个可能事件中包含的解决步骤。

    |事件 ID |Source |描述 |分辨率 |
    |---------|-------|------------|-----------|
    |2133 & 2129 |运行状况服务 |从代理连接到服务失败 |当代理无法直接或通过防火墙/代理服务器与 Azure Monitor 服务通信时，会发生此错误。 验证代理的代理设置或网络防火墙/代理是否允许从计算机到服务的 TCP 流量。|
    |2138 |运行状况服务模块 |代理要求身份验证 |配置代理的代理设置，并指定在代理服务器上进行身份验证所需的用户名/密码。 |
    |2129 |运行状况服务模块 |连接失败/失败 SSL 协商 |检查网络适配器的 "TCP/IP 设置" 和 "代理程序" 代理设置。|
    |2127 |运行状况服务模块 |发送数据失败，错误代码为 |如果只是在一天内定期发生，则可能是可以忽略的随机异常。 监视，了解它的发生频率。 如果经常发生这种情况，请先检查网络配置和代理设置。 如果说明中包含 HTTP 错误代码404，并且它是第一次尝试将数据发送到服务的时间，则它将包含500错误，其中包含内部404错误代码。 404表示找不到，这表示仍在设置新工作区的存储区域。 下一次重试时，数据会按预期成功写入工作区。 HTTP 错误403可能指示权限或凭据问题。 403错误中提供了更多的信息，以帮助解决此问题。|
    |4000 |服务连接器 |DNS 名称解析失败 |计算机无法解析向服务发送数据时使用的 Internet 地址。 这可能是你的计算机上的 DNS 解析器设置、不正确的代理设置或提供程序的暂时性 DNS 问题。 如果定期发生，则可能是由与网络相关的暂时性问题导致的。|
    |4001 |服务连接器 |与服务的连接失败。 |当代理无法直接或通过防火墙/代理服务器与 Azure Monitor 服务通信时，会发生此错误。 验证代理的代理设置或网络防火墙/代理是否允许从计算机到服务的 TCP 流量。|
    |4002 |服务连接器 |服务返回 HTTP 状态代码403以响应查询。 请咨询服务管理员以了解服务的运行状况。 稍后将重试查询。 |此错误是在代理初始注册阶段写入的，你将看到类似于以下内容的 URL： *https://\<> workspaceID/AgentService/AgentTopologyRequest*。 错误代码403表示禁止，可能是由于工作区 ID 或密钥键入错误，或者计算机上的数据和时间不正确引起的。 如果时间比当前时间快/慢 15 分钟，则载入失败。 若要更正此错误，请更新 Windows 计算机的日期和/或时区。|

## <a name="data-collection-issues"></a>数据收集问题

安装代理并将其报告给其配置的工作区或工作区后，它可能会根据启用和定位于计算机的内容，停止接收配置、收集或转发性能、日志或其他数据。 需要确定：

- 它是否为特定的数据类型或工作区中不提供的所有数据？
- 是解决方案指定的数据类型还是作为工作区数据收集配置的一部分指定的？
- 有多少台计算机受到影响？ 是否是向工作区报告的一台或多台计算机？
- 它是否工作，它是否会在一天的特定时间停止，或者是否从未收集？ 
- 使用语法正确的日志搜索查询是正确的吗？ 
- 代理是否曾收到 Azure Monitor 的配置？

故障排除的第一步是确定计算机是否正在发送检测信号事件。

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

如果查询返回结果，则需要确定是否未收集特定数据类型并将其转发给服务。 这可能是因为代理没有从服务接收更新的配置，或是导致代理无法正常运行的其他症状。 执行以下步骤以进一步排除故障。

1. 在计算机上打开提升的命令提示符，然后键入 `net stop healthservice && net start healthservice` 重新启动代理服务。
2. 打开*Operations Manager*事件日志，并从**事件源***运行状况服务*搜索**事件 id** *7023、7024、7025、7028*和*1210* 。  这些事件表明代理已成功接收来自 Azure Monitor 的配置，并且这些事件正在主动监视该计算机。 事件 ID 1210 的事件描述还将在最后一行上指定代理上的监视范围内包含的所有解决方案和见解。  

    ![事件 ID 1210 说明](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. 几分钟后，如果您在查询结果或可视化中看不到预期的数据，则根据您是从解决方案还是从见解中查看数据，从*Operations Manager*事件日志搜索**事件源***运行状况服务*和*运行状况服务模块*并按**事件级别**的*警告*和*错误*进行筛选，以确认它是否已从下表中写入事件。

    |事件 ID |Source |描述 |分辨率 |
    |---------|-------|------------|
    |8000 |运行状况服务 |如果与所收集的性能、事件或其他数据类型相关的工作流无法转发到该服务以引入到工作区，则此事件将指定。 | 源运行状况服务中的事件 ID 2136 与此事件一起写入，并可指示代理无法与服务进行通信，这可能是由于代理和身份验证设置配置错误、网络中断或网络防火墙/代理不允许从计算机到服务的 TCP 流量。| 
    |10102和10103 |运行状况服务模块 |工作流无法解析数据源。 |如果计算机上不存在指定的性能计数器或实例，或者工作区数据设置中定义不正确，则会发生这种情况。 如果这是用户指定的[性能计数器](data-sources-performance-counters.md#configuring-performance-counters)，请验证指定的信息的格式是否正确，以及目标计算机上是否存在该信息。 |
    |26002 |运行状况服务模块 |工作流无法解析数据源。 |如果计算机上不存在指定的 Windows 事件日志，可能会发生这种情况。 如果计算机不应注册此事件日志，则可以安全地忽略此错误，否则，如果这是用户指定的[事件日志](data-sources-windows-events.md#configuring-windows-event-logs)，请验证指定的信息是否正确。 |

