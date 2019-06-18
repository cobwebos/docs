---
title: 如何使用适用于 Windows 的 Log Analytics 代理的问题进行疑难解答 |Microsoft Docs
description: 使用适用于 Windows Azure Monitor 中的 Log Analytics 代理描述症状、 原因和解决方法有关的最常见问题。
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
ms.openlocfilehash: afa4483677336e9a887908a8cccf9590eed27af3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120105"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>如何使用适用于 Windows 的 Log Analytics 代理的问题进行疑难解答 

本文提供了帮助来解决的错误可能会遇到与 Windows Azure Monitor 中的 Log Analytics 代理并建议可能的解决方案来解决这些问题。

如果这些步骤对你均无效，我们还提供了以下支持渠道：

* 具有顶级支持权益的客户可以通过[顶级支持](https://premier.microsoft.com/)提出支持请求。
* 签订了 Azure 支持协议的客户可以在 [Azure 门户](https://manage.windowsazure.com/?getsupport=true)中提出支持请求。
* 请访问 Log Analytics 反馈页面，查看已提交的想法和 bug [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback)或提交新的想法或 bug。 

## <a name="important-troubleshooting-sources"></a>重要的故障排除源

 若要帮助进行故障排除与适用于 Windows 的 Log Analytics 代理相关的问题，代理记录的事件到 Windows 事件日志中，特别是在*应用程序和 Services\Operations Manager*。  

## <a name="connectivity-issues"></a>连接问题

如果代理通过代理服务器或防火墙进行通信，可能阻止了来自源计算机和 Azure Monitor 服务通信的现有的限制。 如果通信受阻，配置错误，注册与工作区可能会失败而尝试安装代理，配置代理后安装程序以报告到其他工作区，或在成功注册之后失败的代理通信。 本部分介绍的方法来解决此类型的 Windows 代理问题。 

仔细检查该防火墙或代理配置为允许以下端口和下表中所述的 Url。 此外确认 HTTP 检查未启用的 web 流量，因为它可能会阻止代理与 Azure Monitor 之间安全的 TLS 通道。  

|代理资源|端口 |Direction |绕过 HTTPS 检查|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |端口 443 |出站|是 |  
|*.oms.opinsights.azure.com |端口 443 |出站|是 |  
|\* .blob.core.windows.net |端口 443 |出站|是 |  
|\* .azure-automation.net |端口 443 |出站|是 |  

所需的 Azure 政府版的防火墙信息，请参阅[Azure 政府版管理](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)。 

有几种方法可以验证是否代理已成功与 Azure Monitor 通信。

- 启用[Azure 日志分析代理运行状况评估](../insights/solution-agenthealth.md)工作区中。 代理运行状况仪表板中，查看**无响应代理的计数**列，以快速查看代理被列出。  

- 运行以下查询来确认代理将检测信号发送到其配置为向报告的工作区。 替换为<ComputerName>计算机的实际名称。

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    如果计算机已成功与服务通信，该查询应返回结果。 如果查询未返回结果，首先验证代理配置为向正确的工作区报告。 如果正确配置，请继续执行步骤 3 和搜索 Windows 事件日志来识别是否有代理日志记录的问题可能会导致无法与 Azure Monitor 进行通信。

- 若要识别连接问题的另一种方法是通过运行**TestCloudConnectivity**工具。 该工具安装文件夹中的代理默认情况下 *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent*。 从提升的命令提示符，导航到的文件夹并运行该工具。 该工具返回的结果和突出显示 （例如，如果它已与一个特定端口/的 URL 已被阻止） 测试失败的位置。 

    ![TestCloudConnection 工具执行结果](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- 筛选器*Operations Manager*由事件日志**事件源** - *运行状况服务模块*，*运行状况服务*，和*Service Connector*并筛选**事件级别***警告*和*错误*以确认其是否写入中的事件下表。 如果是，查看包含的每个可能的事件的解决方法步骤。

    |事件 ID |source |描述 |解决方法 |
    |---------|-------|------------|-----------|
    |2133 & 2129 |运行状况服务 |从代理到服务连接失败 |代理无法直接或通过防火墙/代理服务器与 Azure Monitor 服务通信时，会出现此错误。 验证代理的代理设置或网络防火墙/代理允许从计算机到服务的 TCP 流量。|
    |2138 |运行状况服务模块 |代理要求身份验证 |配置代理的代理设置并指定所需使用代理服务器进行身份验证的用户名/密码。 |
    |2129 |运行状况服务模块 |失败连接/失败 SSL 协商 |检查你的网络适配器的 TCP/IP 设置和代理的代理设置。|
    |2127 |运行状况服务模块 |无法发送数据接收到错误代码 |如果它仅发生在一天中定期，它可能只是随机的异常情况可被忽略。 若要了解它的发生频率的监视器。 如果它通常在一天发生，首先检查您的网络配置和代理设置。 如果它是第一次在代理尝试将数据发送到服务说明内容包括 HTTP 错误代码 404，它包含与内部的 404 错误代码 500 错误。 404 表示未找到，指示新的工作区的存储区域仍在预配。 下一步重试时，数据将成功写入到工作区按预期方式。 HTTP 错误 403 可能指示权限或凭据问题。 没有包含出现 403 错误来帮助解决此问题的详细信息。|
    |4000 |服务连接器 |DNS 名称解析失败 |在计算机无法解析数据发送到服务时使用的 Internet 地址。 这可能是你的计算机、 不正确的代理设置或可能是一个临时的 DNS 问题上与提供商的 DNS 解析器设置。 如果定期发生，它可能造成暂时性的网络相关问题。|
    |4001 |服务连接器 |无法连接到该服务。 |代理无法直接或通过防火墙/代理服务器与 Azure Monitor 服务通信时，会出现此错误。 验证代理的代理设置或网络防火墙/代理允许从计算机到服务的 TCP 流量。|
    |4002 |服务连接器 |服务在查询响应中返回 HTTP 状态代码 403。 检查与服务管理员核实服务的运行状况。 稍后将重试查询。 |在代理的初始注册阶段期间写入此错误，您将看到类似于以下 URL: *https://<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*。 错误代码 403 已禁止的方式，并可能引起的错误输入的工作区 ID 或密钥，或日期和时间不正确的计算机上。 如果时间比当前时间快/慢 15 分钟，则载入失败。 若要更正此问题，更新的日期和/或 Windows 计算机的时区。|

## <a name="data-collection-issues"></a>数据收集问题

后安装了代理，并对其配置的工作区或工作区报告，它可能会停止接收配置、 收集或转发性能、 日志或其他数据到具体取决于启用了哪些服务和目标计算机。 有必要确定：

- 它是特定的数据类型还是不可用的工作区中的所有数据？
- 指定的解决方案，或指定为工作区数据收集配置的一部分的数据类型？
- 多少台计算机会受到影响？ 它是单个还是多台计算机向工作区报告？
- 正在处理和未它停止在一天中的特定时间或它从未收集？ 
- 是正在使用日志搜索查询语法正确？ 
- 已将代理不断收到其配置 Azure Monitor？

故障排除的第一步是确定计算机是否正在发送检测信号事件。

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

如果查询返回结果，然后您需要确定如果特定的数据类型是不会收集，并转发到服务。 原因可能是未从服务或防止代理正常运行的一些其他症状接收更新的配置的代理。 执行以下步骤来进一步解决。

1. 打开提升的命令提示符在计算机上，通过键入重新启动代理服务`net stop healthservice && net start healthservice`。
2. 打开*Operations Manager*事件日志并搜索**事件 Id** *7023、 7024、 7025、 7028*并*1210年*从**事件源***运行状况服务*。  这些事件指示代理已成功接收来自 Azure Monitor 配置，并且它们正在主动监视计算机。 事件 ID 1210 还将指定的最后一个事件描述行的所有解决方案和见解，包括在监视代理上的范围。  

    ![事件 ID 1210 描述](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. 如果在几分钟后看不到查询结果或可视化效果的预期的数据，具体取决于你正在查看从解决方案或见解，数据从*Operations Manager*事件日志中，搜索**事件源***运行状况服务*并*运行状况服务模块*筛选**事件级别***警告*和*错误*以确认它具有下表中所编写的事件。

    |事件 ID |source |描述 |解决方法 |
    |---------|-------|------------|
    |8000 |HealthService |此事件将指定工作流与相关的性能，事件或收集其他数据类型不能转发到该服务的引入到工作区。 | 事件 ID 2136 从源运行状况服务写入与此事件，并且可以指示代理不能与服务通信，可能是由于配置错误的代理和身份验证设置、 网络中断或网络防火墙 /代理不允许从计算机到服务的 TCP 流量。| 
    |10102 和 10103 |运行状况服务模块 |工作流无法解析数据源。 |如果指定的性能计数器或实例的计算机上不存在或未正确定义工作区数据设置中，这可能发生。 如果这是用户指定[性能计数器](data-sources-performance-counters.md#configuring-performance-counters)，验证指定的信息是按照正确的格式，然后在目标计算机上存在。 |
    |26002 |运行状况服务模块 |工作流无法解析数据源。 |如果计算机上不存在指定的 Windows 事件日志，这可能发生。 此错误可以安全地忽略如果计算机不应具有此注册，否则如果这是用户指定的事件日志[事件日志](data-sources-windows-events.md#configuring-windows-event-logs)，验证指定的信息是否正确。 |

