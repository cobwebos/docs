---
title: Application Insights 的版本批注 | Microsoft 文档
description: 为 Application Insights 中的指标资源管理器图表添加部署或版本标记。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mbullwin
ms.openlocfilehash: e3ec202ba6126b150fb78c76591682f163018661
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604548"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>为 Application Insights 中的指标图表添加批注

上的批注[指标资源管理器](../../azure-monitor/app/metrics-explorer.md)的图表显示在其中部署新的生成或其他重要事件。 批注可让轻松查看所做的更改是否有任何对您的应用程序性能的影响。 它们可以自动创建由[Azure 管道](https://docs.microsoft.com/azure/devops/pipelines/tasks/)生成系统。 也可以通过 PowerShell 创建批注，用于标记所要处理的任何事件。

> [!NOTE]
> 本文反映了已弃用的**经典指标体验**。 批注目前仅在经典体验和 **[工作簿](../../azure-monitor/app/usage-workbooks.md)** 中可用。 若要了解有关当前指标体验的详细信息，请参阅[Azure 指标资源管理器的高级功能](../../azure-monitor/platform/metrics-charts.md)。

![批注的示例](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>版本与 Azure 管道生成的注释

版本批注是一项功能的基于云的 Azure 管道服务的 Azure DevOps。

### <a name="install-the-annotations-extension-one-time"></a>安装批注扩展（一次性操作）
为了能够创建版本批注，您将需要安装 Visual Studio Marketplace 中提供的众多 Azure DevOps 扩展之一。

1. 登录到您[Azure DevOps](https://azure.microsoft.com/services/devops/)项目。
   
1. 在 Visual Studio Marketplace[版本批注扩展](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)页上，选择你的 Azure DevOps 组织，并选择**安装**将扩展添加到你的 Azure DevOps 组织。
   
   ![选择 Azure DevOps 的组织，然后选择安装。](./media/annotations/1-install.png)
   
只需安装一次为你的 Azure DevOps 组织扩展。 现在可以在组织中配置任何项目的版本的注释。

### <a name="configure-release-annotations"></a>配置版本批注

为每个 Azure 管道版本模板创建单独的 API 密钥。

1. 登录到[Azure 门户](https://portal.azure.com)并打开监视你的应用程序的 Application Insights 资源。 如果你没有帐户，或者[创建新的 Application Insights 资源](../../azure-monitor/app/app-insights-overview.md)。
   
1. 打开“API 访问”  选项卡并复制 **Application Insights ID**。
   
   ![在 API 访问权限下将复制应用程序 id。](./media/annotations/2-app-id.png)

1. 在单独的浏览器窗口中，打开或创建管理 Azure 管道部署的发布模板。
   
1. 选择**添加任务**，然后选择**Application Insights 版本批注**菜单中的任务。
   
   ![选择添加任务，然后选择 Application Insights 版本批注。](./media/annotations/3-add-task.png)
   
1. 下**应用程序 ID**，粘贴从复制的应用程序见解 ID **API 访问**选项卡。
   
   ![粘贴 Application Insights ID](./media/annotations/4-paste-app-id.png)
   
1. 返回在 Application Insights **API 访问权限**窗口中，选择**创建 API 密钥**。 
   
   ![在 API 访问权限选项卡，选择创建 API 密钥。](./media/annotations/5-create-api-key.png)
   
1. 在中**创建 API 密钥**窗口中，键入说明，选择**编写注释**，然后选择**生成密钥**。 复制新密钥。
   
   ![在创建 API 密钥窗口中，键入描述，选择写入注释，然后选择生成密钥。](./media/annotations/6-create-api-key.png)
   
1. 在版本模板窗口中，在**变量**选项卡上，选择**添加**若要创建新的 API 密钥的变量定义。

1. 下**名称**，输入`ApiKey`，然后在**值**，粘贴从复制的 API 密钥**API 访问**选项卡。
   
   ![Azure DevOps 变量选项卡中，选择添加名称变量 ApiKey，并粘贴 API 密钥值下。](./media/annotations/7-paste-api-key.png)
   
1. 选择**保存**将模板保存在主发布模板窗口中。

## <a name="view-annotations"></a>查看批注
现在，每当使用发布模板部署新版本，批注是发送到 Application Insights。 在图中显示批注**指标资源管理器**。

选择任一批注标记 （浅灰色箭头） 可打开了解此版本，包括请求者、 源控制分支、 发布管道和环境的详细信息。

![选择版本批注标记。](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>通过 PowerShell 创建自定义批注
可以使用[CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)从 GitHub 创建批注的任何您喜欢，而无需使用 Azure DevOps 过程的 PowerShell 脚本。 

1. 创建的本地副本[CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)。
   
1. 使用在前面的过程中的步骤以获取你的应用程序见解 ID 并创建从 Application Insights API 密钥**API 访问**选项卡。
   
1. 调用 PowerShell 脚本使用以下代码，尖括号中的占位符替换为你的值。 `-releaseProperties`都是可选的。 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

可以修改脚本，例如过去创建批注。

## <a name="next-steps"></a>后续步骤

* [创建工作项](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [使用 PowerShell 自动化](../../azure-monitor/app/powershell.md)
