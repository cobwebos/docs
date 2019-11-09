---
title: 使用 Azure Monitor 中的应用程序更改分析查找 web 应用问题 |Microsoft Docs
description: 使用 Azure Monitor 中的应用程序更改分析排查 Azure 应用服务中的实时站点应用程序问题。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: ed297a1005f67a14db1da15aba2c47c98e83df9c
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73884997"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>使用 Azure Monitor 中的应用程序更改分析（预览版）

发生实时站点问题或服务中断时，快速确定根本原因至关重要。 标准的监视解决方案可能会针对问题发出警报。 它们甚至可以指出哪个组件发生了故障。 但是，此警报不一定总能立即解释故障的原因。 你知道你的站点在五分钟前还一切正常，但现在它已中断。 那么，过去 5 分钟发生了什么？ Azure Monitor 中的应用程序更改分析就是为了解答此类问题而开发的。

更改分析构建在 [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) 的强大功能基础之上，可让你洞察 Azure 应用程序的更改，以提高可观察性并减少 MTTR（平均修复时间）。

> [!IMPORTANT]
> 更改分析目前以预览版提供。 此预览版不附带服务级别协议。 不建议对生产工作负荷使用此版本。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="overview"></a>概述

更改分析可以检测从基础结构层到应用程序部署中的各种更改。 它是订阅级别的 Azure 资源提供程序，可检查订阅中的资源更改。 更改分析提供各种数据诊断工具，帮助用户了解哪些更改可能导致出现了问题。

下图演示了更改分析的体系结构：

![演示更改分析如何获取更改数据并将其提供给客户端工具的体系结构示意图](./media/change-analysis/overview.png)

当前的更改分析集成到应用服务 web 应用中的**诊断和解决问题**体验，并在 Azure 门户中作为独立的边栏选项卡提供。
请参阅本文后面的 "*查看 Azure 中所有资源的更改*" 部分以访问更改分析边栏选项卡和*web 应用功能的更改分析*部分，以便在 web 应用门户中使用它。

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure 资源管理器跟踪的属性更改

更改分析使用 [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) 提供托管应用程序的 Azure 资源在不同时间的更改历史记录。 可以检测到管理的设置，例如托管标识、平台操作系统升级和主机名。

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure 资源管理器代理设置更改
设置（例如 IP 配置规则、SSL 设置和扩展版本）在 ARG 中尚不可用，因此更改分析查询并安全地计算这些更改，以提供有关应用中所更改内容的更多详细信息。 这些信息在 Azure 资源图中不可用，但即将推出。

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Web 应用部署和配置（来宾内更改）中的更改

更改分析每隔 4 小时捕获一次应用程序的部署和配置状态。 例如，它可以检测应用程序环境变量的更改。 该工具会计算差异，并显示已发生更改的内容。 与资源管理器更改不同，代码部署更改信息可能不会立即在该工具中提供。 若要查看更改分析中的最新更改，请选择“立即扫描更改”。

![“立即扫描更改”按钮的屏幕截图](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>依赖项更改

对资源依赖项的更改也可能会导致 Web 应用出现问题。 例如，如果某个 Web 应用调用 Redis 缓存，Redis 缓存 SKU 可能会影响该 Web 应用的性能。 若要检测依赖项的更改，更改分析将检查 Web 应用的 DNS 记录。 它通过这种方式识别所有应用组件中可能导致出现问题的更改。
目前支持以下依赖项：
- Web 应用
- Azure 存储
- Azure SQL

### <a name="enablement"></a>实现
需要将 "ChangeAnalysis" 资源提供程序注册到 Azure 资源管理器跟踪的属性的订阅中，代理设置将数据更改为 "可用"。 输入 Web 应用诊断和解决问题或打开 "更改分析独立" 边栏选项卡时，将自动注册此资源提供程序。 它不会为你的订阅提供任何性能和成本实现。
对于 web 应用中的 web 应用更改，在 web 应用中扫描代码文件需要单独启用。 有关更多详细信息，请参阅本文后面*的 "诊断和解决问题" 工具部分中的 "启用更改分析"* 。

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

## <a name="change-analysis-for-the-web-apps-feature"></a>适用于 Web 应用的更改分析功能

在 Azure Monitor 中，更改分析也内置于自助服务**诊断和解决问题**中。 可以从应用服务应用程序的“概述”页访问此体验。

![“概述”按钮和“诊断并解决问题”按钮的屏幕截图](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>在“诊断并解决问题”工具中启用更改分析

1. 选择“可用性和性能”。

    ![“可用性和性能”故障排除选项的屏幕截图](./media/change-analysis/availability-and-performance.png)

1. 选择“应用程序更改”。 请注意，“应用程序崩溃”中也提供了该功能。

   ![“应用程序崩溃”按钮的屏幕截图](./media/change-analysis/application-changes.png)

1. 若要启用更改分析，请选择“立即启用”。

   ![“应用程序崩溃”选项的屏幕截图](./media/change-analysis/enable-changeanalysis.png)

1. 启用“更改分析”并选择“保存”。 该工具显示应用服务计划下的所有 web 应用。 您可以使用计划级别开关为某个计划下的所有 web 应用启用更改分析。

    ![“启用更改分析”用户界面的屏幕截图](./media/change-analysis/change-analysis-on.png)


1. 若要访问更改分析，请选择“诊断并解决问题” **“可用性和性能”** “应用程序崩溃”。 >  >  你将看到一个图表，其中汇总了一段时间内的更改类型以及这些更改的详细信息。 默认情况下，将显示过去24小时内的更改，以帮助解决即时问题。

     ![更改差异视图的屏幕截图](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>大规模启用更改分析

如果订阅包含大量的 Web 应用，在 Web 应用级别启用该服务的做法就不够有效。 运行以下脚本以启用订阅中的所有 Web 应用。

先决条件：
* PowerShell Az 模块。 请按照[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)中的说明操作

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

- 为 [Azure 应用服务应用](azure-web-apps.md)启用 Application Insights。
- 为 [Azure VM 和 Azure 虚拟机规模集的 IIS 托管应用](azure-vm-vmss-apps.md)启用 Application Insights。
- 详细了解有助于增强更改分析功能的 [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)。
