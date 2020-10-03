---
title: 使用 Azure Monitor 中的应用程序更改分析查找 web 应用问题 |Microsoft Docs
description: 使用 Azure Monitor 中的应用程序更改分析排查 Azure 应用服务中的实时站点应用程序问题。
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: 9abca58aa79e0924281ab69314271f2aeca6bfa6
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667534"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>使用 Azure Monitor 中的应用程序更改分析（预览版）

发生实时站点问题或服务中断时，快速确定根本原因至关重要。 标准的监视解决方案可能会针对问题发出警报。 它们甚至可以指出哪个组件发生了故障。 但是，此警报不一定总能立即解释故障的原因。 你知道你的站点在五分钟前还一切正常，但现在它已中断。 那么，过去 5 分钟发生了什么？ Azure Monitor 中的应用程序更改分析就是为了解答此类问题而开发的。

更改分析构建在 [Azure Resource Graph](../../governance/resource-graph/overview.md) 的强大功能基础之上，可让你洞察 Azure 应用程序的更改，以提高可观察性并减少 MTTR（平均修复时间）。

> [!IMPORTANT]
> 更改分析目前以预览版提供。 此预览版不附带服务级别协议。 不建议对生产工作负荷使用此版本。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="overview"></a>概述

更改分析可以检测从基础结构层到应用程序部署中的各种更改。 它是订阅级别的 Azure 资源提供程序，可检查订阅中的资源更改。 更改分析提供各种数据诊断工具，帮助用户了解哪些更改可能导致出现了问题。

下图演示了更改分析的体系结构：

![演示更改分析如何获取更改数据并将其提供给客户端工具的体系结构示意图](./media/change-analysis/overview.png)

## <a name="data-sources"></a>数据源

适用于 Azure 资源管理器跟踪的属性的应用程序更改分析查询，代理配置和来宾内的 web 应用更改。 此外，该服务会跟踪资源依赖项更改，以对应用程序进行端到端的诊断和监视。

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure 资源管理器跟踪的属性更改

更改分析使用 [Azure Resource Graph](../../governance/resource-graph/overview.md) 提供托管应用程序的 Azure 资源在不同时间的更改历史记录。 可以检测到管理的设置，例如托管标识、平台操作系统升级和主机名。

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure 资源管理器代理设置更改

设置（例如 IP 配置规则、TLS 设置和扩展版本）在 Azure 资源图中尚不可用，因此更改分析查询并安全地计算这些更改，以提供应用程序中更改的内容的更多详细信息。

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Web 应用部署和配置的更改（来宾中的更改）

更改分析每隔 4 小时捕获一次应用程序的部署和配置状态。 例如，它可以检测应用程序环境变量的更改。 该工具会计算差异，并显示已发生更改的内容。 与资源管理器更改不同，代码部署更改信息可能不会立即在该工具中提供。 若要查看更改分析中的最新更改，请选择 " **刷新**"。

![“立即扫描更改”按钮的屏幕截图](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>依赖项更改

对资源依赖项的更改也可能会导致 Web 应用出现问题。 例如，如果某个 Web 应用调用 Redis 缓存，Redis 缓存 SKU 可能会影响该 Web 应用的性能。 若要检测依赖项的更改，更改分析将检查 Web 应用的 DNS 记录。 它通过这种方式识别所有应用组件中可能导致出现问题的更改。
目前支持以下依赖项：
- Web 应用
- Azure 存储
- Azure SQL

## <a name="application-change-analysis-service"></a>应用程序更改分析服务

应用程序更改分析服务计算并聚合上述数据源中的更改数据。 它提供一组分析，使用户能够轻松地浏览所有资源更改，并识别故障排除或监视上下文中的相关更改。
需将“Microsoft.ChangeAnalysis”资源提供程序注册到某个订阅，然后才可使用 Azure 资源管理器的跟踪属性和代理设置更改数据。 当你输入 Web 应用 "诊断和解决问题" 工具或显示 "更改分析独立" 选项卡时，将自动注册此资源提供程序。 它不会为你的订阅提供任何性能或成本实现。 当你为 web 应用启用更改分析 (或启用) 的 "诊断和解决问题" 工具时，它将对 web 应用造成性能影响，无需支付费用。
对于 Web 应用的来宾中更改，需要单独的支持才能在 Web 应用中扫描代码文件。 有关详细信息，请参阅本文后面 [的诊断和解决问题工具](#application-change-analysis-in-the-diagnose-and-solve-problems-tool) 一节中的更改分析。

## <a name="visualizations-for-application-change-analysis"></a>应用程序更改分析的可视化效果

### <a name="standalone-ui"></a>独立 UI

在 Azure Monitor 中，有一个独立的窗格可供更改分析查看对应用程序依赖项和资源的见解的所有更改。

在 Azure 门户上的搜索栏中搜索 "更改分析" 以启动体验。

![在 Azure 门户中搜索“更改分析”的屏幕截图](./media/change-analysis/search-change-analysis.png)

所选订阅下的所有资源都显示在过去24小时内的更改中。 若要优化页面负载性能，服务一次显示10个资源。 单击 "下一页" 以查看更多资源。 我们正在努力消除此限制。

![Azure 门户中“更改分析”边栏选项卡的屏幕截图](./media/change-analysis/change-analysis-standalone-blade.png)

单击资源即可查看其所有更改。 如果需要，请向下钻取以查看 json 格式的更改详细信息和见解。

![更改详细信息的屏幕截图](./media/change-analysis/change-details.png)

有关任何反馈，请使用边栏选项卡或电子邮件中的 "发送反馈" 按钮 changeanalysisteam@microsoft.com 。

![“更改分析”边栏选项卡中反馈按钮的屏幕截图](./media/change-analysis/change-analysis-feedback.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Web 应用诊断和解决问题

在 Azure Monitor 中，更改分析也已内置到自助式“诊断并解决问题”体验中。**** 可以从应用服务应用程序的“概述”页访问此体验。****

![“概述”按钮和“诊断并解决问题”按钮的屏幕截图](./media/change-analysis/change-analysis.png)

### <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>诊断和解决问题工具中的应用程序更改分析

应用程序更改分析是 Web 应用程序中的独立检测器，诊断并解决问题工具。 它还在 **应用程序崩溃** 和 **Web 应用关闭检测程序**中进行聚合。 输入 "诊断并解决问题" 工具时，将自动注册 **ChangeAnalysis** 资源提供程序。 按照以下说明启用 web 应用来宾内更改跟踪。

1. 选择“可用性和性能”。****

    ![“可用性和性能”故障排除选项的屏幕截图](./media/change-analysis/availability-and-performance.png)

2. 选择“应用程序更改”。**** 此功能在 **应用程序崩溃**中也可用。

   ![“应用程序崩溃”按钮的屏幕截图](./media/change-analysis/application-changes.png)

3. 链接导致应用程序更改 Aalysis UI 范围限定于 web 应用。 如果未启用 "来宾内的 web 应用" 更改跟踪，请单击标题以获取文件和应用设置更改。

   ![“应用程序崩溃”选项的屏幕截图](./media/change-analysis/enable-changeanalysis.png)

4. 启用“更改分析”并选择“保存”。******** 该工具显示应用服务计划下的所有 web 应用。 可以使用计划级别开关，为某个计划下的所有 Web 应用启用更改分析。

    ![“启用更改分析”用户界面的屏幕截图](./media/change-analysis/change-analysis-on.png)

5. 更改数据还可在选择 **Web 应用关闭** 和 **应用程序崩溃** 检测程序中使用。 此时会显示一个图形，其中汇总了在不同时间发生的更改类型，以及有关这些更改的详细信息。 默认情况下会显示过去 24 小时的更改，方便你解决即时问题。

     ![更改差异视图的屏幕截图](./media/change-analysis/change-view.png)



### <a name="virtual-machine-diagnose-and-solve-problems"></a>虚拟机诊断和解决问题

请参阅诊断并解决虚拟机的问题工具。  转到 **故障排除工具**，浏览页面，并选择 " **分析最近的更改** " 以查看虚拟机上的更改。

![VM 的屏幕截图诊断和解决问题](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![故障排除工具中的更改分析器](./media/change-analysis/analyze-recent-changes.png)

### <a name="activity-log-change-history"></a>活动日志更改历史记录
活动日志中的 " [查看更改历史记录](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log#view-change-history) " 功能将调用应用程序更改分析服务后端，以获取与操作关联的更改。 用于直接调用[Azure 资源关系图](https://docs.microsoft.com/azure/governance/resource-graph/overview)的**更改历史记录**，但已通过交换后端来调用应用程序更改分析，因此，返回的更改将包括来自[azure 资源关系图](https://docs.microsoft.com/azure/governance/resource-graph/overview)的资源级别更改、 [azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)的资源属性以及 PaaS 服务（如应用服务 web 应用）的来宾内更改。 为了使应用程序更改分析服务能够扫描用户订阅中的更改，需要注册资源提供程序。 第一次输入 " **更改历史记录** " 选项卡时，该工具将自动开始注册 **ChangeAnalysis** 资源提供程序。 注册后，将立即提供 **Azure 资源关系图** 的更改，并涵盖过去14天的时间。 在订阅完成后大约4小时后，其他源的更改将可用。

![活动日志更改历史记录集成](./media/change-analysis/activity-log-change-history.png)

### <a name="vm-insights-integration"></a>VM Insights 集成
已启用 [VM Insights](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) 的用户可以查看其虚拟机中的更改，这些更改可能会导致度量值图表（如 CPU 或内存）中出现任何峰值，并清楚地引发了原因。 更改数据集成在 VM Insights 导航栏中。 用户可以查看 VM 中是否发生了任何更改，并单击 " **调查更改** " 以在应用程序更改分析独立 UI 中查看更改详细信息。

[![VM insights 集成](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)



## <a name="enable-change-analysis-at-scale"></a>大规模启用更改分析

如果订阅包含大量的 Web 应用，在 Web 应用级别启用该服务的做法就不够有效。 运行以下脚本以启用订阅中的所有 Web 应用。

先决条件：

- PowerShell Az 模块。 请按照[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps?view=azps-2.6.0)中的说明操作

运行以下脚本：

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"

# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```

## <a name="troubleshoot"></a>疑难解答

### <a name="having-trouble-registering-microsoftchange-analysis-resource-provider-from-change-history-tab"></a>注册 Microsoft 时遇到问题。请从 "更改历史记录" 选项卡更改分析资源提供程序
如果是第一次在其与应用程序更改分析集成后查看更改历史记录，则会看到它会自动注册资源提供程序 **ChangeAnalysis**。 在极少数情况下，可能会由于以下原因而失败：

- **你的权限不足，无法注册 ChangeAnalysis 资源提供程序**。 此错误消息表示当前订阅中的角色不具有与之关联的 **Microsoft. 支持/注册/操作** 范围。 如果你不是订阅的所有者，并且通过同事获取共享访问权限，则可能会发生这种情况。 即，查看对资源组的访问权限。 若要解决此问题，可以联系订阅所有者来注册 **ChangeAnalysis** 资源提供程序。 这可以通过订阅 Azure 门户来完成 **|资源提供程序** ，并 ```Microsoft.ChangeAnalysis``` 在 UI 中搜索和注册，或通过 Azure PowerShell 或 Azure CLI。

    通过 PowerShell 注册资源提供程序： 
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **注册 ChangeAnalysis 资源提供程序失败**。 此消息意味着 UI 发送请求以注册资源提供程序时，会立即失败，这与权限问题无关。 可能是暂时性的 internet 连接问题。 请尝试刷新页面并检查你的 internet 连接。 如果错误仍然存在，请联系 changeanalysishelp@microsoft.com

- **此时间比预期时间长**。 此消息表示注册花费的时间超过2分钟。 这种情况很罕见，但并不一定表示出现了问题。 可以中转到 " **订阅" |** 用于检查 **ChangeAnalysis** 资源提供程序注册状态的资源提供程序。 可以尝试使用 UI 注销、重新注册或刷新，以查看是否有帮助。 如果问题仍然存在，请联系 changeanalysishelp@microsoft.com 以获得支持。
    ![排除 RP 注册花费的时间过长](./media/change-analysis/troubleshoot-registration-taking-too-long.png)



## <a name="next-steps"></a>后续步骤

- 为 [Azure 应用服务应用](azure-web-apps.md)启用 Application Insights。
- 为 [Azure VM 和 Azure 虚拟机规模集的 IIS 托管应用](azure-vm-vmss-apps.md)启用 Application Insights。
- 详细了解有助于增强更改分析功能的 [Azure Resource Graph](../../governance/resource-graph/overview.md)。
