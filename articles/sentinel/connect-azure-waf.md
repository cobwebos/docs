---
title: 将 Azure Web 应用程序防火墙 (WAF) 数据连接到 Azure Sentinel
description: 了解如何将 Azure WAF 数据连接到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 05/07/2020
ms.author: yelevin
ms.openlocfilehash: 8f21b415ef36442d6ac1aac518cd1327f70b8927
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88263924"
---
# <a name="connect-data-from-azure-web-application-firewall-waf"></a>从 Azure Web 应用程序防火墙 (WAF) 连接数据

Web 应用程序已逐渐成为利用常见已知漏洞的恶意攻击的目标。 Azure Web 应用程序防火墙 (WAF) 提供对 Web 应用程序的集中保护，使其免受常见攻击和威胁，如代码注入和跨站点脚本。 可以将 azure WAF 部署到 [Azure 应用程序网关](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview) 服务、 [azure 前门](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview) 服务和 [AZURE 内容分发网络 (CDN) ](https://docs.microsoft.com/azure/web-application-firewall/cdn/cdn-overview) WAF 策略 (当前在公共预览版中的) 。
你可以将 Azure WAF 日志连接到 Azure Sentinel，使你能够查看工作簿中的日志数据，使用它创建自定义警报，并将其合并以改善调查。

## <a name="prerequisites"></a>必备条件

- 你必须具有 Azure Sentinel 工作区的读取和写入权限。

## <a name="connect-to-azure-waf"></a>连接到 Azure WAF

### <a name="instructions-tab"></a>说明选项卡

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从数据连接器库中选择 " **Azure web 应用程序防火墙 (WAF") ** ，然后选择预览窗格中的 " **打开连接器" 页面** 。

1. 选择要连接其日志的 WAF 资源类型的链接– **打开 "应用程序网关资源" >**、" **打开前门资源" >**，或打开 " **内容交付网络 (CDN) WAF 策略 >** –在资源列表的屏幕中，从列表中选择 WAF 资源。

    1. 在 WAF 资源的导航菜单中，选择 " **诊断设置**"。

    1. 选择列表底部的 " **+ 添加诊断设置** "。

    1. 在 " **诊断设置** " 屏幕的 " **诊断设置名称** " 字段中键入名称。

    1. 单击 " **发送到 Log Analytics** " 复选框。 其中将显示两个新字段。 选择相关 **订阅** ，并 **Log Analytics 工作区** (Azure Sentinel 所在的位置) 。

    1. 单击要摄取其日志的规则类型的复选框。 每种资源类型的建议：

        | 资源 | 要选择用于引入的日志 |
        |----------|------------------------------|
        | 应用程序网关 | ApplicationGatewayAccessLog<br>ApplicationGatewayFirewallLog |
        | Front Door          | FrontdoorAccessLog<br>FrontdoorWebApplicationFirewallLog |
        | CDN WAF 策略      | WebApplicationFirewallLogs |
        |

    1. 选择“保存”。

### <a name="next-steps-tab"></a>后续步骤选项卡

- 若要深入了解 Azure WAF 日志数据，请参阅与 **azure web 应用程序防火墙** 数据连接器捆绑在一起的建议工作簿、查询示例和分析规则模板。

- 若要在 **日志**中查询 Azure WAF 数据，请在 "查询" 窗口中键入 **AzureDiagnostics** 。

> [!NOTE]
>
> 使用这个特定的数据连接器，在过去两周内引入数据时，数据连接器库中的连接状态 (指示器和数据类型) 名称旁的连接图标将显示为 " *已连接* (绿色) 。 两周后，如果没有数据引入，连接器将显示为 "已断开连接"。 数据经历的时间越多， *连接* 状态就返回。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Azure WAF 日志连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
