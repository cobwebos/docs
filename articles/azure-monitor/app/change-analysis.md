---
title: 使用 Azure Monitor 中的应用程序更改分析查找 web 应用问题 |Microsoft Docs
description: 使用 Azure Monitor 中的应用程序更改分析来对 Azure App Service 上的实时网站上的应用程序问题进行故障排除。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: 3805d7b39c25bcb213a1d4f110161dcd00eb3630
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678253"
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

当前的更改分析集成到应用服务 web 应用中的**诊断和解决问题**。 若要在 web 应用中启用更改检测和查看更改，请参阅本文后面的对*Web 应用功能的更改分析*部分。

### <a name="azure-resource-manager-deployment-changes"></a>Azure 资源管理器部署更改

使用[Azure 资源关系图](https://docs.microsoft.com/azure/governance/resource-graph/overview)，更改分析提供了一条历史记录，说明托管应用程序的 Azure 资源在一段时间内的变化情况。 例如，更改分析可以检测 IP 配置规则、托管标识和 SSL 设置的更改。 因此，如果在 web 应用中添加了标记，则更改分析将反映此更改。 只要在 Azure 订阅中启用了 `Microsoft.ChangeAnalysis` 资源提供程序，就可以使用此信息。

### <a name="changes-in-web-app-deployment-and-configuration"></a>Web 应用部署和配置中的更改

更改分析每4小时捕获一次应用程序的部署和配置状态。 例如，它可以检测应用程序环境变量中的更改。 该工具会计算差异并显示已更改的内容。 与资源管理器更改不同的是，在工具中可能不会立即提供代码部署更改信息。 若要查看更改分析中的最新更改，请选择 "**立即扫描更改**"。

!["立即扫描更改" 按钮的屏幕截图](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>依赖项更改

更改资源依赖项也会导致 web 应用出现问题。 例如，如果 web 应用调用 Redis 缓存，则 Redis 缓存 SKU 可能会影响 web 应用性能。 为了检测依赖项中的更改，更改分析检查 web 应用的 DNS 记录。 通过这种方式，它可以识别可能导致问题的所有应用组件中的更改。
目前支持以下依赖项：
- Web 应用
- Azure 存储器
- Azure SQL


## <a name="change-analysis-for-the-web-apps-feature"></a>更改 Web 应用功能的分析

在 Azure Monitor 中，更改分析当前内置于自助服务**诊断和解决问题**。 从应用服务应用程序的 "**概述**" 页访问此体验。

!["概述" 按钮和 "诊断并解决问题" 按钮的屏幕截图](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>启用 "诊断和解决问题" 工具中的更改分析

1. 选择 "**可用性和性能**"。

    !["可用性和性能" 疑难解答选项的屏幕截图](./media/change-analysis/availability-and-performance.png)

1. 选择**应用程序更改**。 此功能在**应用程序崩溃**中也不可用。

   !["应用程序崩溃" 按钮的屏幕截图](./media/change-analysis/application-changes.png)

1. 若要启用更改分析，请选择 "**立即启用**"。

   !["应用程序崩溃" 选项的屏幕截图](./media/change-analysis/enable-changeanalysis.png)

1. 打开 "**更改分析**"，然后选择 "**保存**"。

    !["启用更改分析" 用户界面的屏幕截图](./media/change-analysis/change-analysis-on.png)


1. 若要访问更改分析，请选择 "**诊断和解决** > **可用性和性能** > **应用程序崩溃**的问题。 你将看到一个图表，其中汇总了一段时间内的更改类型以及这些更改的详细信息：

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
