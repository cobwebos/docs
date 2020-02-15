---
title: 使用 Azure Monitor 中的应用程序更改分析查找 web 应用问题 |Microsoft Docs
description: 使用 Azure Monitor 中的应用程序更改分析来对 Azure App Service 上的实时网站上的应用程序问题进行故障排除。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: f2602dbee12f82c32ab3a3c2ec0566d8dfbeaa83
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211820"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>在 Azure Monitor 中使用应用程序更改分析（预览版）

发生实时站点问题或中断时，快速确定根本原因至关重要。 标准监视解决方案可能会提醒你问题。 它们甚至可能表明哪个组件发生了故障。 但此警报不会立即解释失败的原因。 你知道，你的网站在五分钟前就会正常运行。 过去五分钟内发生了哪些变化？ 这是应用程序更改分析旨在 Azure Monitor 的答案的问题。

利用[Azure 资源图](https://docs.microsoft.com/azure/governance/resource-graph/overview)的强大功能，更改分析提供对 azure 应用程序更改的深入了解，以提高可观察性并减少 MTTR （平均修复时间）。

> [!IMPORTANT]
> 更改分析目前处于预览阶段。 提供此预览版本时没有服务级别协议。 不建议将此版本用于生产工作负荷。 某些功能可能不受支持，或可能具有受限制的功能。 有关详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="overview"></a>概述

更改分析可检测各种类型的更改，从基础结构层一直到应用程序部署。 它是一个订阅级别的 Azure 资源提供程序，用于检查订阅中的资源更改。 更改分析提供各种诊断工具的数据，以帮助用户了解哪些更改可能导致了问题。

下图说明了更改分析的体系结构：

![有关更改分析如何获取更改数据并将其提供给客户端工具的体系结构关系图](./media/change-analysis/overview.png)

当前的更改分析集成到应用服务 web 应用中的**诊断和解决问题**体验，并在 Azure 门户中作为独立选项卡提供。
请参阅本文后面的 "*查看 Azure 中所有资源的更改*" 部分以访问更改分析边栏选项卡和*web 应用功能的更改分析*部分，以便在 web 应用门户中使用它。

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure 资源管理器跟踪的属性更改

使用[Azure 资源关系图](https://docs.microsoft.com/azure/governance/resource-graph/overview)，更改分析提供了一条历史记录，说明托管应用程序的 Azure 资源在一段时间内的变化情况。 可以检测到管理的设置，例如托管标识、平台操作系统升级和主机名。

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure 资源管理器代理设置更改
设置（例如 IP 配置规则、SSL 设置和扩展版本）在 ARG 中尚不可用，因此更改分析查询并对这些更改进行安全计算，以便在应用程序中更改的内容中提供更多详细信息。 这些信息在 Azure 资源图中不可用，但即将推出。

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Web 应用部署和配置（来宾内更改）中的更改

更改分析每4小时捕获一次应用程序的部署和配置状态。 例如，它可以检测应用程序环境变量中的更改。 该工具会计算差异并显示已更改的内容。 与资源管理器更改不同的是，在工具中可能不会立即提供代码部署更改信息。 若要查看更改分析中的最新更改，请选择 "**立即扫描更改**"。

!["立即扫描更改" 按钮的屏幕截图](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>依赖项更改

更改资源依赖项也会导致 web 应用出现问题。 例如，如果 web 应用调用 Redis 缓存，则 Redis 缓存 SKU 可能会影响 web 应用性能。 为了检测依赖项中的更改，更改分析检查 web 应用的 DNS 记录。 通过这种方式，它可以识别可能导致问题的所有应用组件中的更改。
目前支持以下依赖项：
- Web 应用
- Azure 存储
- Azure SQL

### <a name="enablement"></a>实现
需要将 "ChangeAnalysis" 资源提供程序注册到 Azure 资源管理器跟踪的属性的订阅中，代理设置将数据更改为 "可用"。 当你输入 Web 应用 "诊断和解决问题" 工具或显示 "更改分析独立" 选项卡时，将自动注册此资源提供程序。 它不会为你的订阅提供任何性能和成本实现。 当你为 web 应用启用更改分析（或在 "诊断和解决问题" 工具中启用）时，它将对 web 应用造成性能影响，无需支付费用。
对于 web 应用中的 web 应用更改，在 web 应用中扫描代码文件需要单独启用。 有关详细信息，请参阅本文后面[的 "诊断和解决问题" 工具一节中的 "启用更改分析"](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#enable-change-analysis-in-the-diagnose-and-solve-problems-tool) 。


## <a name="viewing-changes-for-all-resources-in-azure"></a>查看 Azure 中所有资源的更改
在 Azure Monitor 中，有一个独立的边栏选项卡，用于更改分析，以查看具有见解和应用程序依赖项资源的所有更改。

在 Azure 门户上的搜索栏中搜索 "更改分析" 以启动边栏选项卡。

![Azure 门户中搜索更改分析的屏幕截图](./media/change-analysis/search-change-analysis.png)

选择 "资源组和资源" 开始查看更改。

![Azure 门户中的更改分析边栏选项卡的屏幕截图](./media/change-analysis/change-analysis-standalone-blade.png)

你可以查看托管你的应用程序的见解和相关的依赖项资源。 此视图旨在以应用程序为中心，以便开发人员排除问题。

当前支持的资源包括：
- 虚拟机
- 虚拟机规模集
- Azure 网络资源
- 具有来宾内文件跟踪和环境变量更改的 Web 应用

有关任何反馈，请使用边栏选项卡中的 "发送反馈" 按钮或电子邮件 changeanalysisteam@microsoft.com。

![更改分析边栏选项卡中 "反馈" 按钮的屏幕截图](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>更改 Web 应用功能的分析

在 Azure Monitor 中，更改分析也内置于自助服务**诊断和解决问题**中。 从应用服务应用程序的 "**概述**" 页访问此体验。

!["概述" 按钮和 "诊断并解决问题" 按钮的屏幕截图](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>启用 "诊断和解决问题" 工具中的更改分析

1. 选择 "**可用性和性能**"。

    !["可用性和性能" 疑难解答选项的屏幕截图](./media/change-analysis/availability-and-performance.png)

1. 选择**应用程序更改**。 此功能在**应用程序崩溃**中也不可用。

   !["应用程序崩溃" 按钮的屏幕截图](./media/change-analysis/application-changes.png)

1. 若要启用更改分析，请选择 "**立即启用**"。

   !["应用程序崩溃" 选项的屏幕截图](./media/change-analysis/enable-changeanalysis.png)

1. 打开 "**更改分析**"，然后选择 "**保存**"。 该工具显示应用服务计划下的所有 web 应用。 您可以使用计划级别开关为某个计划下的所有 web 应用启用更改分析。

    !["启用更改分析" 用户界面的屏幕截图](./media/change-analysis/change-analysis-on.png)


1. 若要访问更改分析，请选择 "**诊断和解决** > **可用性和性能** > **应用程序崩溃**的问题。 你将看到一个图表，其中汇总了一段时间内的更改类型以及这些更改的详细信息。 默认情况下，将显示过去24小时内的更改，以帮助解决即时问题。

     !["更改差异" 视图的屏幕截图](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>大规模启用更改分析

如果你的订阅包含大量 web 应用，则在 web 应用级别启用此服务的效率将会降低。 运行以下脚本以启用订阅中的所有 web 应用。

先决条件：
* PowerShell Az Module。 按照[安装 Azure PowerShell 模块中的](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)说明进行操作

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



## <a name="next-steps"></a>后续步骤

- 为[Azure 应用服务应用](azure-web-apps.md)启用 Application Insights。
- 启用[AZURE VM 和 azure 虚拟机规模集的 IIS 托管应用](azure-vm-vmss-apps.md)的 Application Insights。
- 详细了解[Azure 资源图](https://docs.microsoft.com/azure/governance/resource-graph/overview)，有助于进行电源更改分析。
