---
title: 如何排查 Log Analytics Windows 代理的问题 | Microsoft Docs
description: 描述 Azure Monitor 中 Log Analytics Windows 代理最常见问题的症状、原因和解决方法。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2019
ms.author: magoedte
ms.openlocfilehash: 9df389b6e6a73530c9bbf5a2187d6735946e309f
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249777"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>如何排查 Log Analytics Windows 代理的问题 

本文介绍如何排查可能遇到的 Azure Monitor 中的 Log Analytics Windows 代理的相关错误，并提供可能的解决方法建议。

如果这些步骤对你均无效，我们还提供了以下支持渠道：

* 具有顶级支持权益的客户可以通过[顶级支持](https://premier.microsoft.com/)提出支持请求。
* 签订了 Azure 支持协议的客户可以在 [Azure 门户](https://manage.windowsazure.com/?getsupport=true)中提出支持请求。
* 请访问 Log Analytics 反馈页面，查看已提交的想法和 bug [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback)或提交新的想法或 bug。 

## <a name="important-troubleshooting-sources"></a>重要的故障排除源

 为了帮助排查 Log Analytics Windows 代理相关的问题，该代理会将事件记录到 Windows 事件日志，具体而言，是 *Application and Services\Operations Manager* 下的日志。  

## <a name="connectivity-issues"></a>连接问题

如果代理通过代理服务器或防火墙通信，某些限制可能会阻止源计算机和 Azure Monitor 服务发起的通信。 如果通信受阻或者配置不当，在尝试安装代理或者配置代理的后期设置以向其他工作区报告时，注册到工作区可能会失败，或者在成功注册后代理通信失败。 本部分将介绍排查此类 Windows 代理问题的方法。 

请仔细检查防火墙或代理是否配置为允许下表中所述的端口和 URL。 此外，请确认没有为 Web 流量启用 HTTP 检查，因为它可能会阻止代理与 Azure Monitor 之间的安全 TLS 通道。  

|代理资源|端口 |Direction |绕过 HTTPS 检查|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |端口 443 |出站|是 |  
|*.oms.opinsights.azure.com |端口 443 |出站|是 |  
|\* .blob.core.windows.net |端口 443 |出站|是 |  
|\* .azure-automation.net |端口 443 |出站|是 |  

有关 Azure 政府版所需的防火墙信息, 请参阅[Azure 政府版管理](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)。 

可通过多种方法验证代理是否能够成功与 Azure Monitor 通信。

- 在工作区中启用 [Azure Log Analytics 代理运行状况评估](../insights/solution-agenthealth.md)。 在“代理运行状况”仪表板中，查看“无响应代理的计数”列，以快速确认该代理是否已列出。   

- 运行以下查询来确认代理是否会将检测信号发送到为其配置的目标报告工作区。 请将 `<ComputerName>` 替换为计算机的实际名称。

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    如果计算机可成功与服务通信，则该查询应会返回结果。 如果查询未返回结果，请先验证代理是否配置为向正确的工作区报告。 如果配置正确，请转到步骤 3 并搜索 Windows 事件日志，以确定代理是否在日志中指出了哪种问题导致它无法与 Azure Monitor 通信。

- 识别连接问题的另一种方法是运行 **TestCloudConnectivity** 工具。 默认情况下，该工具会连同代理一起安装在 *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent* 文件夹中。 在权限提升的命令提示符下，导航到该文件夹并运行该工具。 该工具会返回结果，并突出显示测试在哪个位置失败（例如，指出问题是否与某个已阻止的特定端口/URL 相关）。 

    ![TestCloudConnection 工具的执行结果](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- 按**事件源**  - “运行状况服务模块”、“运行状况服务”和“服务连接器”筛选“Operations Manager”事件日志，并按**事件级别** -“警告”和“错误”进行筛选，以确认代理是否写入了下表中所述的事件。       如果已写入，请查看针对每个可能的事件提供的解决方法步骤。

    |事件 ID |Source |描述 |解决方法 |
    |---------|-------|------------|-----------|
    |2133 和 2129 |运行状况服务 |从代理连接到服务失败 |如果代理无法直接或者通过防火墙/代理服务器来与 Azure Monitor 服务通信，则可能会发生此错误。 验证该代理程序的代理设置，或者网络防火墙/代理是否允许将该计算机的 TCP 流量发送到服务。|
    |2138 |运行状况服务模块 |代理要求身份验证 |配置该代理程序的代理设置，并指定在代理服务器上进行身份验证所需的用户名/密码。 |
    |2129 |运行状况服务模块 |连接失败/SSL 协商失败 |检查网络适配器的 TCP/IP 设置和代理程序的代理设置。|
    |2127 |运行状况服务模块 |发送数据失败并收到错误代码 |如果此问题只是在某一天中定期发生，原因可能是出现随机异常；可忽略此问题。 通过监视来了解问题发生的频率。 如果在一整天经常发生，请先检查网络配置和代理设置。 如果说明中包含 HTTP 错误代码 404，并且这是代理首次尝试将数据发送到服务，则错误消息中会包含 500 错误和 404 内部错误代码。 404 表示“未找到”，即，仍在预配新工作区的存储区域。 下次重试时，数据将成功按预期写入到工作区。 HTTP 错误 403 可能表示出现权限或凭据问题。 403 错误会包含更多信息来帮助排查问题。|
    |4000 |服务连接器 |DNS 名称解析失败 |计算机无法解析在向服务发送数据时使用的 Internet 地址。 原因可能是计算机上的 DNS 解析程序设置有问题、代理设置不正确，或者提供商出现了暂时性的 DNS 问题。 如果此错误定期发生，原因可能是存在暂时性的网络相关问题。|
    |4001 |服务连接器 |无法连接到服务。 |如果代理无法直接或者通过防火墙/代理服务器来与 Azure Monitor 服务通信，则可能会发生此错误。 验证该代理程序的代理设置，或者网络防火墙/代理是否允许将该计算机的 TCP 流量发送到服务。|
    |4002 |服务连接器 |服务返回 HTTP 状态代码403以响应查询。 请咨询服务管理员以了解服务的运行状况。 稍后将重试查询。 |此错误是在代理初始注册阶段写入的, 你将看到类似于以下内容的 URL: *https://\<> workspaceID/AgentService/AgentTopologyRequest*。 错误代码403表示禁止, 可能是由于工作区 ID 或密钥键入错误, 或者计算机上的数据和时间不正确引起的。 如果时间比当前时间快/慢 15 分钟，则载入失败。 若要更正此错误, 请更新 Windows 计算机的日期和/或时区。|

## <a name="data-collection-issues"></a>数据收集问题

代理已安装并可以向配置的一个或多个工作区报告后，根据启用的设置以及面向计算机的内容，它可能会停止接收配置、收集性能、日志或其他数据或者将这些数据转发到服务。 需要确定：

- 工作区中是特定的数据类型不可用，还是所有数据都不可用？
- 该数据类型是由解决方案指定的，还是指定为工作区数据收集配置的一部分？
- 有多少台计算机受到影响？ 是有一台还是多台计算机向工作区报告？
- 代理是否只是在一天的特定时间停止，而在其他时间可保持工作，或者，它是否从未收集过数据？ 
- 使用的日志搜索查询在语法上是否正确？ 
- 代理是否曾经从 Azure Monitor 接收过其配置？

故障排除的第一步是确定计算机是否发送检测信号事件。

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

如果查询返回了结果，则你需要确定是否未收集特定的数据类型并将其转发到服务。 原因可能是代理未从服务接收更新的配置，或者其他某种症状阻止了代理正常运行。 执行以下步骤进一步进行故障排除。

1. 在计算机上打开权限提升的命令提示符，并键入 `net stop healthservice && net start healthservice` 重启代理服务。
2. 打开“Operations Manager”事件日志，并在**事件源**“运行状况服务”中搜索**事件 ID** 7023、7024、7025、7028 和 1210。      这些事件表示代理可成功从 Azure Monitor 接收配置，并且它们正在监视计算机。 事件 ID 1210 的事件说明还会在最后一行中指定代理监视范围内的所有解决方法和见解。  

    ![事件 ID 1210 说明](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. 如果在几分钟后，查询结果或可视化效果中未按预期显示数据，请根据你查看的是解决方法还是见解中的数据，在“Operations Manager”事件日志中，搜索**事件源**“运行状况服务”和“运行状况服务模块”，并按**事件级别**“警告”和“错误”进行筛选，以确认代理是否写入了下表中所述的事件。     

    |事件 ID |Source |描述 |解决方法 |
    |---------|-------|------------|
    |8000 |HealthService |此事件将指定与性能、事件或收集的其他数据类型相关的工作流是否无法将这些数据转发到服务，以引入到工作区。 | 来自源运行状况服务的事件 ID 2136 将连同此事件一起写入，可能表示代理无法与服务通信，原因可能是代理和身份验证设置的配置不当、网络中断，或者网络防火墙/代理不允许将计算机的 TCP 流量发送到服务。| 
    |10102 和 10103 |运行状况服务模块 |工作流无法解析数据源。 |如果指定的性能计数器或实例在计算机上不存在，或者在工作区数据设置中未正确定义，则可能会发生此错误。 如果这是用户指定的[性能计数器](data-sources-performance-counters.md#configuring-performance-counters)，请验证指定的信息是否遵循正确的格式，并在目标计算机上存在。 |
    |26002 |运行状况服务模块 |工作流无法解析数据源。 |如果指定的 Windows 事件日志在计算机上不存在，则可能会发生此错误。 如果预期不需要在计算机上此注册此事件日志，则可以安全忽略此错误；如果这是用户指定的[事件日志](data-sources-windows-events.md#configuring-windows-event-logs)，请验证指定的信息是否正确。 |

