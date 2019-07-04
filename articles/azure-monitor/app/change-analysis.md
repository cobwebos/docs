---
title: 使用 Azure Monitor 中的应用程序更改分析以查找 web 应用的问题 |Microsoft Docs
description: 使用 Azure Monitor 中应用程序更改分析 Azure 应用服务上的实时站点上的应用程序问题进行疑难解答。
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 45df8f9e57223ea60a11c6af2187d362184cae2b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443354"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>在 Azure Monitor 中使用应用程序更改分析 （预览版）

实时站点问题或服务中断发生时，快速确定根本原因是关键。 标准的监视解决方案可能会发出警报的问题。 它们甚至可能指示哪个组件发生故障。 但此警报不会始终立即解释故障的原因。 您知道您的站点过五分钟之前，并且现在它已损坏。 那么，过去 5 分钟发生了什么？ 这才是问题所在的应用程序更改分析主要用于解答在 Azure 监视器中。

上的强大功能构建[Azure 资源 Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)，更改分析可深入了解 Azure 应用程序更改来提高可观察性并减少 MTTR （平均修复时间）。

> [!IMPORTANT]
> 更改分析当前处于预览状态。 此预览版提供，没有服务级别协议。 此版本不建议用于生产工作负荷。 某些功能可能不受支持，或可能功能受限。 有关详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="overview"></a>概述

更改分析检测到各种类型的更改，从应用程序部署到的基础结构层。 它是检查在订阅中的资源更改的订阅级别的 Azure 资源提供程序。 更改分析提供了各种诊断工具可以帮助用户了解哪些更改的数据可能会导致问题。

下图说明更改 Analysis 的体系结构：

![如何更改分析获取更改数据并将其提供给客户端工具的体系结构示意图](./media/change-analysis/overview.png)

当前更改分析集成到**诊断并解决问题**应用服务 web 应用中体验。 若要启用更改检测和 web 应用中查看更改，请参阅*Web 应用功能更改分析*本文后面的部分。

### <a name="azure-resource-manager-deployment-changes"></a>Azure 资源管理器部署更改

使用[Azure 资源 Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)，更改分析，提供了如何随时间改变的 Azure 资源的托管应用程序的历史记录。 更改分析可以检测到，例如，更改 IP 配置规则、 管理的标识和 SSL 设置。 因此如果一个标记添加到 web 应用，更改分析将反映的更改。 此信息才可用，只要`Microsoft.ChangeAnalysis`资源提供程序启用 Azure 订阅中。

### <a name="changes-in-web-app-deployment-and-configuration"></a>Web 应用部署和配置中的更改

更改分析捕获的应用程序每隔 4 小时的部署和配置状态。 它可以检测，例如，应用程序的环境变量中的更改。 该工具计算的差异，并显示所发生的更改。 与资源管理器更改不同代码部署更改信息可能不是立即出现在该工具中。 若要更改分析中查看最新更改，请选择**扫描更改现在**。

!["现在更改扫描"按钮的屏幕截图](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>依赖项发生更改

对资源的依赖项的更改也会导致问题的 web 应用中。 例如，如果 web 应用连接到 Redis 缓存时，Redis 缓存的 SKU 可能会影响 web 应用程序的性能。 若要检测的依赖项中的更改，更改分析，请检查 web 应用的 DNS 记录。 在这种方式，它标识可能会导致问题的所有应用组件中的更改。

## <a name="change-analysis-for-the-web-apps-feature"></a>更改分析 Web 应用功能

在 Azure 监视器中，更改分析当前生成到自助服务**诊断并解决问题**体验。 访问此体验**概述**在应用服务应用程序页。

!["概述"按钮的屏幕截图和"诊断并解决问题"按钮](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>启用更改分析中诊断并解决问题的工具

1. 选择**可用性和性能**。

    !["可用性和性能"故障排除选项的屏幕截图](./media/change-analysis/availability-and-performance.png)

1. 选择**应用程序更改**。 不是该功能现也已推出**应用程序崩溃**。

   !["应用程序崩溃"按钮的屏幕截图](./media/change-analysis/application-changes.png)

1. 若要启用更改分析，请选择**立即启用**。

   !["应用程序崩溃"选项的屏幕截图](./media/change-analysis/enable-changeanalysis.png)

1. 开启**更改 Analysis** ，然后选择**保存**。

    !["启用更改分析"用户界面的屏幕截图](./media/change-analysis/change-analysis-on.png)


1. 若要访问更改分析，请选择**诊断并解决问题** > **可用性和性能** > **应用程序崩溃**。 你将看到一个图，以及这些更改的详细信息的时间内汇总的更改的类型：

     ![更改差异视图的屏幕截图](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>启用在规模较大更改分析

如果你的订阅包括多个 web 应用，启用 web 应用程序级别服务很低。 在这种情况下，请按照这些说明。

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>注册你的订阅更改分析资源提供程序

1. 注册更改分析功能标志 （预览版）。 功能标志处于预览状态，因为您需要进行注册，使其可见到你的订阅：

   1. 打开 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)。

      ![更改 Cloud Shell 的屏幕截图](./media/change-analysis/cloud-shell.png)

   1. 命令行程序将类型更改为**PowerShell**。

      ![更改 Cloud Shell 的屏幕截图](./media/change-analysis/choose-powershell.png)

   1. 运行以下 PowerShell 命令：

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```

1. 注册订阅更改分析资源提供程序。

   - 转到**订阅**，并选择你想要更改服务中启用的订阅。 然后选择资源提供程序：

        ![显示如何注册更改分析资源提供程序的屏幕截图](./media/change-analysis/register-rp.png)

       - 选择**Microsoft.ChangeAnalysis**。 然后在页面的顶部，选择**注册**。

       - 启用资源提供程序后，你可以设置 web 应用来检测部署级别的更改隐藏的标记。 若要设置隐藏的标记，请执行下面的说明 **，无法提取更改分析信息**。

   - 或者，可以使用 PowerShell 脚本注册资源提供程序：

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        若要使用 PowerShell 设置隐藏的标记的 web 应用上，运行以下命令：

        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > 添加隐藏的标记后，仍可能需要等待最多 4 个小时才能开始看到更改。 结果被延迟，因为更改分析扫描你的 web 应用仅每隔 4 小时。 4 小时计划限制扫描的性能影响。

## <a name="next-steps"></a>后续步骤

- 监视应用服务通过更有效地[启用 Application Insights 功能](azure-web-apps.md)Azure 监视器中。
- 详细了解如何[Azure 资源 Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)，这可帮助 power 更改分析。
