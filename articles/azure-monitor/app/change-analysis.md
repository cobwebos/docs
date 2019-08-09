---
title: 使用 Azure Monitor 中的应用程序更改分析查找 web 应用问题 |Microsoft Docs
description: 使用 Azure Monitor 中的应用程序更改分析排查 Azure 应用服务中的实时站点应用程序问题。
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: a08fc7d7822b4aeddafb588fdb73e86559ce2b12
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68849172"
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

目前，更改分析已应用服务 Web 应用中的集成到“诊断并解决问题”体验。 若要在 Web 应用中启用更改检测和查看更改，请参阅本文稍后的“适用于 Web 应用的更改分析功能”部分。

### <a name="azure-resource-manager-deployment-changes"></a>Azure 资源管理器部署更改

更改分析使用 [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) 提供托管应用程序的 Azure 资源在不同时间的更改历史记录。 例如，更改分析可以检测 IP 配置规则、托管标识和 SSL 设置的更改。 因此，如果将某个标记添加到 Web 应用，更改分析将反映这种更改。 只要在 Azure 订阅中启用 `Microsoft.ChangeAnalysis` 资源提供程序，就会提供此信息。

### <a name="changes-in-web-app-deployment-and-configuration"></a>Web 应用部署和配置的更改

更改分析每隔 4 小时捕获一次应用程序的部署和配置状态。 例如，它可以检测应用程序环境变量的更改。 该工具会计算差异，并显示已发生更改的内容。 与资源管理器更改不同，代码部署更改信息可能不会立即在该工具中提供。 若要查看更改分析中的最新更改，请选择“立即扫描更改”。

![“立即扫描更改”按钮的屏幕截图](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>依赖项更改

对资源依赖项的更改也可能会导致 Web 应用出现问题。 例如，如果某个 Web 应用调用 Redis 缓存，Redis 缓存 SKU 可能会影响该 Web 应用的性能。 若要检测依赖项的更改，更改分析将检查 Web 应用的 DNS 记录。 它通过这种方式识别所有应用组件中可能导致出现问题的更改。
目前支持以下依赖项:
- Web Apps
- Azure 存储
- Azure SQL


## <a name="change-analysis-for-the-web-apps-feature"></a>适用于 Web 应用的更改分析功能

在 Azure Monitor 中，更改分析目前已内置到自助式“诊断并解决问题”体验中。 可以从应用服务应用程序的“概述”页访问此体验。

![“概述”按钮和“诊断并解决问题”按钮的屏幕截图](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>在“诊断并解决问题”工具中启用更改分析

1. 选择“可用性和性能”。

    ![“可用性和性能”故障排除选项的屏幕截图](./media/change-analysis/availability-and-performance.png)

1. 选择“应用程序更改”。 请注意，“应用程序崩溃”中也提供了该功能。

   ![“应用程序崩溃”按钮的屏幕截图](./media/change-analysis/application-changes.png)

1. 若要启用更改分析，请选择“立即启用”。

   ![“应用程序崩溃”选项的屏幕截图](./media/change-analysis/enable-changeanalysis.png)

1. 启用“更改分析”并选择“保存”。

    ![“启用更改分析”用户界面的屏幕截图](./media/change-analysis/change-analysis-on.png)


1. 若要访问更改分析，请选择“诊断并解决问题” > “可用性和性能” > “应用程序崩溃”。 此时会显示一个图形，其中汇总了在不同时间发生的更改类型，以及有关这些更改的详细信息：

     ![更改差异视图的屏幕截图](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>大规模启用更改分析

如果订阅包含大量的 Web 应用，在 Web 应用级别启用该服务的做法就不够有效。 对于这种情况，请遵照以下说明。

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>为订阅注册更改分析资源提供程序

1. 注册更改分析功能标志（预览版）。 由于功能标志目前为预览版，因此需要将它注册才能让订阅看到它：

   1. 打开 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)。

      ![更改 Cloud Shell 的屏幕截图](./media/change-analysis/cloud-shell.png)

   1. 将 shell 类型更改为**PowerShell**。

      ![更改 Cloud Shell 的屏幕截图](./media/change-analysis/choose-powershell.png)

   1. 运行以下 PowerShell 命令：

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```

1. 为订阅注册更改分析资源提供程序。

   - 转到“订阅”，并选择要在更改服务中启用的订阅。 然后选择资源提供程序：

        ![显示如何注册更改分析资源提供程序的屏幕截图](./media/change-analysis/register-rp.png)

       - 选择“Microsoft.ChangeAnalysis”。 然后在页面顶部，选择“注册”。

       - 启用资源提供程序后，可在 Web 应用中设置一个隐藏的标记，用于检测部署级别的更改。 若要设置隐藏的标记，请遵照“无法提取更改分析信息”中的说明。

   - 或者，可以使用 PowerShell 脚本注册资源提供程序：

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        若要使用 PowerShell 在 Web 应用中设置隐藏的标记，请运行以下命令：

        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > 添加隐藏的标记后，可能仍需等待最长 4 个小时，才能开始看到更改。 结果之所以延迟出现，是因为更改分析每隔 4 小时扫描一次 Web 应用。 采用 4 小时计划可以限制扫描对性能造成的影响。

## <a name="next-steps"></a>后续步骤

- 为[Azure 应用服务应用](azure-web-apps.md)启用 Application Insights。
- 启用[AZURE VM 和 azure 虚拟机规模集的 IIS 托管应用](azure-vm-vmss-apps.md)的 Application Insights。
- 详细了解有助于增强更改分析功能的 [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)。
