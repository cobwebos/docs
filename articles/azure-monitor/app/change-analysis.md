---
title: Azure 监视器应用程序更改分析-发现分析，更改可能会影响实时站点问题/中断与 Azure Monitor 应用程序的更改 |Microsoft Docs
description: 对 Azure 应用服务上使用 Azure Monitor 应用程序更改分析的应用程序实时站点问题进行故障排除
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 5bd3816e65398283de85b4551a137b3f97db4cc7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226328"
---
# <a name="application-change-analysis-public-preview"></a>应用程序更改分析 （公共预览版）

当实时站点问题/中断发生时，快速确定根本原因是关键。 标准监视解决方案可帮助您快速确定问题，并且通常，即使哪个组件发生故障。 但这始终不会导致立即发生失败的原因的理由。 你的站点过五分钟之前，现在它已损坏。 在过去 5 分钟内发生什么变化？ 这是新功能 Azure Monitor 应用程序更改分析旨在回答的问题。 通过基于的强大功能构建[Azure 资源 Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)应用程序更改分析提供了深入了解 Azure 应用程序更改来提高可观察性并减少 MTTR （平均时间修复）。

> [!IMPORTANT]
> Azure 监视器应用程序更改分析当前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="overview-of-change-analysis-service"></a>更改分析服务概述
更改分析服务检测到各种类型的基础结构层从应用程序部署到的更改。 它是订阅级别的 Azure 资源提供程序，用于到订阅中资源更改看起来并提供各种诊断工具来帮助用户了解哪些更改的数据可能会导致问题。

下图说明了更改分析服务的体系结构：![有关如何更改分析服务获取的体系结构关系图更改数据，并向客户端工具提供数据](./media/change-analysis/overview.png)

当前该工具已集成到应用服务 web 应用诊断并解决问题的体验。 请参阅*为应用服务 Web 应用程序更改 Analysis service*节介绍了如何启用和查看到 web 应用所做的更改。

### <a name="azure-resource-manager-deployment-changes"></a>Azure 资源管理器部署更改
利用[Azure 资源 Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)更改分析工具提供了如何随时间改变的 Azure 资源的托管应用程序的历史记录。 例如，如果 web 应用程序必须添加到其中一个标记，更改将反映在更改分析工具。
此信息是始终可用，只要`Microsoft.ChangeAnalysis`资源提供程序是登记到 Azure 订阅。

### <a name="web-application-deployment-and-configuration-changes"></a>Web 应用程序部署和配置更改
更改分析工具捕获部署和配置状态的应用程序每隔 4 小时计算差异和显示所发生的更改。 此类更改的示例包括应用程序的环境变量更改、 IP 配置规则更改、 托管服务标识更改、 SSL 设置的更改等。
与不同的资源管理器更改此类型的更改信息可能无法立即出现在该工具中。 若要查看最新更改，请在工具中使用现在更改扫描按钮。

![更改扫描的屏幕截图现在按钮中诊断并解决问题的应用服务 web 应用的更改分析集成工具](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>依赖项发生更改
依赖关系资源也可能是问题的原因。 例如，如果 web 应用连接到 Redis 缓存时，web 应用程序的性能可能会受到由 Redis 缓存的 SKU。 通过深入了解 web 应用 DNS 记录更改分析服务还提供了依赖项更改的信息来标识可能会导致问题的应用的所有组件中的更改。


## <a name="change-analysis-service-for-app-services-web-app"></a>更改应用服务 Web 应用程序的分析服务

Azure 监视器应用程序更改分析当前内置到自助服务**诊断并解决问题**体验，可以从访问哪一**概述**部分中的 Azure 应用服务应用程序：

![Azure 应用服务的屏幕截图概述页上概述按钮周围的红框和诊断并解决问题按钮](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-diagnose-and-solve-problems-tool"></a>启用更改分析中诊断并解决问题的工具

1. 选择**可用性和性能**

    ![可用性和性能故障排除选项的屏幕截图](./media/change-analysis/availability-and-performance.png)

2. 单击**应用程序崩溃**磁贴。

   ![与应用程序崩溃磁贴的屏幕截图](./media/change-analysis/application-crashes-tile.png)

3. 若要启用**更改 Analysis**选择**立即启用**。

   ![可用性和性能故障排除选项的屏幕截图](./media/change-analysis/application-crashes.png)

4. 若要充分利用完整更改分析功能集**更改 Analysis**，**扫描检测代码改动**，并**始终打开**到**上**然后选择**保存**。

    ![Azure 应用服务启用更改分析用户界面的屏幕截图](./media/change-analysis/change-analysis-on.png)

    如果**更改分析**是启用，你将能够检测资源级别的更改。 如果**扫描代码更改**是启用，你将还请参阅部署文件和站点配置更改。 启用**始终打开**将优化更改扫描的性能，但可能会导致附加成本从计费角度来看。

5.  启用的所有内容后，选择**诊断并解决问题** > **可用性和性能** > **应用程序崩溃**将允许你访问更改分析体验。 在关系图将汇总的这些更改随时间的详细信息以及发生的更改的类型：

     ![更改差异视图的屏幕截图](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-service-at-scale"></a>启用在规模较大的更改分析服务
如果订阅中有许多 web 应用，启用每个 web 应用程序级别上的服务将效率低下。 以下是一些备用的加入说明。

#### <a name="registering-change-analysis-resource-provider-for-your-subscription"></a>注册更改分析资源提供程序为你的订阅

1. 注册更改分析预览版功能标志

    由于此功能处于预览状态，您需要首先注册到你的订阅可见的功能标志。
    - 打开 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)。

    ![更改 Azure Cloud Shell 的屏幕截图](./media/change-analysis/cloud-shell.png)

    - Shell 类型更改为 PowerShell:

    ![更改 Azure Cloud Shell 的屏幕截图](./media/change-analysis/choose-powershell.png)

    - 运行以下 PowerShell 命令：

    ``` PowerShell

    Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to onboard
    Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
    Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag

    ```

2. 更改分析资源提供程序注册订阅

    - 导航到订阅，选择要登记更改服务的订阅，然后单击资源提供程序：

        ![从订阅边栏选项卡注册更改分析 RP 的屏幕快照](./media/change-analysis/register-rp.png)

    - 选择*Microsoft.ChangeAnalysis*然后单击*注册*页面顶部。

    - 上架资源提供程序后，请按照中的说明 *，无法提取更改分析信息*下面启用部署在 web 应用上设置隐藏的标记级别更改检测 web 应用上的。

3. 或者到上面的步骤 2 中，可以注册资源提供程序通过 PowerShell 脚本：

    ```PowerShell
    Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
    ```

4. 若要使用 PowerShell 的 web 应用上设置隐藏的标记，请运行以下命令：

    ```powershell
    $webapp=Get-AzWebApp -Name <name_of_your_webapp>
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
    ```

> [!NOTE]
> 隐藏的标记添加后，你可能仍需要最初等待最多 4 小时，以便能够在第一次查看更改。 这是因为 4 小时 freqeuncy 更改分析服务用来扫描 web 应用，又限制了扫描的性能影响。

## <a name="next-steps"></a>后续步骤

- 提高 Azure 应用服务的监视[通过启用 Application Insights 功能](azure-web-apps.md)的 Azure Monitor。
- 增强的对理解[Azure 资源 Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)从而可帮助 power Azure Monitor 应用程序更改分析。
