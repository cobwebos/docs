---
title: Application Insights 的版本批注 | Microsoft 文档
description: 为 Application Insights 中的指标资源管理器图表添加部署或版本标记。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/01/2019
ms.openlocfilehash: f9ab1f1ef2b64c07a3b0c8ddf93d6ef4293517db
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73668113"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>为 Application Insights 中的指标图表添加批注

[指标资源管理器](../../azure-monitor/app/metrics-explorer.md)图表上的批注显示将新版本部署到了何处，或者显示其他重要事件。 使用批注可让轻松查看更改是否对应用程序的性能产生了任何影响。 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/tasks/) 生成系统可自动创建批注。 也可以通过 PowerShell 创建批注，用于标记所要处理的任何事件。

> [!NOTE]
> 本文反映了已弃用的**经典指标体验**。 批注目前仅在经典体验和 **[工作簿](../../azure-monitor/app/usage-workbooks.md)** 中可用。 若要详细了解当前的指标体验，请参阅 [Azure 指标资源管理器的高级功能](../../azure-monitor/platform/metrics-charts.md)。

![批注的示例](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure Pipelines 生成中的版本批注

版本批注是 Azure DevOps 的基于云的 Azure Pipelines 服务功能。

### <a name="install-the-annotations-extension-one-time"></a>安装批注扩展（一次性操作）
若要创建版本批注，必须安装 Visual Studio Marketplace 中提供的多个 Azure DevOps 扩展中的一个。

1. 登录到 [Azure DevOps](https://azure.microsoft.com/services/devops/) 项目。
   
1. 在 Visual Studio Marketplace 的[版本批注扩展](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)页上选择你的 Azure DevOps 组织，然后选择“安装”将该扩展添加到你的 Azure DevOps 组织。
   
   ![选择 Azure DevOps 组织，然后选择“安装”。](./media/annotations/1-install.png)
   
只需为 Azure DevOps 组织安装该扩展一次。 现在，可为组织中的任何项目配置版本批注。

### <a name="configure-release-annotations"></a>配置版本批注

为每个 Azure Pipelines 发布模板单独创建一个 API 密钥。

1. 登录到 [Azure 门户](https://portal.azure.com)并打开负责监视应用程序的 Application Insights 资源。 或者，如果你没有，请[创建一个新的 Application Insights 资源](../../azure-monitor/app/app-insights-overview.md)。
   
1. 打开“API 访问”选项卡并复制 **Application Insights ID**。
   
   ![在“API 访问”下，复制应用程序 ID。](./media/annotations/2-app-id.png)

1. 在另一个浏览器窗口中，打开或创建用于管理 Azure Pipelines 部署的发布模板。
   
1. 添加“添加任务”，然后从菜单中选择“Application Insights 版本批注”任务。
   
   ![选择“添加任务”，然后选择“Application Insights 版本批注”。](./media/annotations/3-add-task.png)

   > [!NOTE]
   > 发布批注任务当前仅支持基于 Windows 的代理;它不会在 Linux、macOS 或其他类型的代理上运行。
   
1. 在“应用程序 ID”下，粘贴从“API 访问”选项卡复制的 Application Insights ID。
   
   ![粘贴 Application Insights ID](./media/annotations/4-paste-app-id.png)
   
1. 返回 Application Insights 的“API 访问”窗口，选择“创建 API 密钥”。 
   
   ![在“API 访问”选项卡中，选择“创建 API 密钥”。](./media/annotations/5-create-api-key.png)
   
1. 在“创建 API 密钥”窗口中键入说明，选择“编写批注”，然后选择“生成密钥”。 复制新密钥。
   
   ![在“创建 API 密钥”窗口中键入说明，选择“编写批注”，然后选择“生成密钥”。](./media/annotations/6-create-api-key.png)
   
1. 在“发布模板”窗口中的“变量”选项卡上，选择“添加”以创建新 API 密钥的变量定义。

1. 在“名称”下输入 **，然后在“值”下粘贴从“API 访问”选项卡复制的 API 密钥。** `ApiKey`
   
   ![在“Azure DevOps 变量”选项卡中选择“添加”，将变量命名为 ApiKey，然后在“值”下面粘贴 API 密钥。](./media/annotations/7-paste-api-key.png)
   
1. 在“发布模板”主窗口中选择“保存”以保存模板。

## <a name="view-annotations"></a>查看批注
现在，每当使用发布模板部署新版本时，就会将批注发送到 Application Insights。 批注显示在“指标资源管理器”中的图表上。

选择任一批注标记（浅灰色箭头）可打开有关该版本的详细信息，包括请求者、源控制分支、发布管道和环境。

![选择版本批注标记。](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>通过 PowerShell 创建自定义批注
可以使用 GitHub 中的 [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell 脚本，通过所需的任何流程创建批注，而无需使用 Azure DevOps。 

1. 创建 [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) 的本地副本。
   
1. 使用上述过程中的步骤获取 Application Insights ID，并通过 Application Insights 的“API 访问”选项卡创建 API 密钥。
   
1. 使用以下代码调用 PowerShell 脚本（请将带尖括号的占位符替换为你自己的值）。 `-releaseProperties` 是可选的。 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

可以修改脚本，例如，为以往的内容创建批注。

## <a name="next-steps"></a>后续步骤

* [创建工作项](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [使用 PowerShell 自动化](../../azure-monitor/app/powershell.md)
