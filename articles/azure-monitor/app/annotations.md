---
title: Application Insights 的版本批注 | Microsoft 文档
description: 为 Application Insights 中的指标资源管理器图表添加部署或版本标记。
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: 8f6ffed8ae2039ec36ec8bd86518e6b359da65ec
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093028"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>为 Application Insights 中的指标图表添加批注

批注显示部署新生成的位置或其他重要事件。 使用批注可让轻松查看更改是否对应用程序的性能产生了任何影响。 [Azure Pipelines](/azure/devops/pipelines/tasks/) 生成系统可自动创建批注。 也可以通过 PowerShell 创建批注，用于标记所要处理的任何事件。

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure Pipelines 生成中的版本批注

版本批注是 Azure DevOps 的基于云的 Azure Pipelines 服务功能。

### <a name="install-the-annotations-extension-one-time"></a>安装批注扩展（一次性操作）

若要创建版本批注，必须安装 Visual Studio Marketplace 中提供的多个 Azure DevOps 扩展中的一个。

1. 登录到 [Azure DevOps](https://azure.microsoft.com/services/devops/) 项目。
   
1. 在 Visual Studio Marketplace 的[版本批注扩展](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)页上选择你的 Azure DevOps 组织，然后选择“安装”将该扩展添加到你的 Azure DevOps 组织。****
   
   ![选择 Azure DevOps 组织，然后选择“安装”。](./media/annotations/1-install.png)
   
只需为 Azure DevOps 组织安装该扩展一次。 现在，可为组织中的任何项目配置版本批注。

### <a name="configure-release-annotations"></a>配置版本批注

为每个 Azure Pipelines 发布模板单独创建一个 API 密钥。

1. 登录到 [Azure 门户](https://portal.azure.com)并打开负责监视应用程序的 Application Insights 资源。 或者，如果你没有，请[创建一个新的 Application Insights 资源](../../azure-monitor/app/app-insights-overview.md)。
   
1. 打开“API 访问”**** 选项卡并复制 **Application Insights ID**。
   
   ![在“API 访问”下，复制应用程序 ID。](./media/annotations/2-app-id.png)

1. 在另一个浏览器窗口中，打开或创建用于管理 Azure Pipelines 部署的发布模板。
   
1. 添加“添加任务”，然后从菜单中选择“Application Insights 版本批注”任务。********
   
   ![选择“添加任务”，然后选择“Application Insights 版本批注”。](./media/annotations/3-add-task.png)

   > [!NOTE]
   > 发布批注任务当前仅支持基于 Windows 的代理;它不会在 Linux、macOS 或其他类型的代理上运行。
   
1. 在“应用程序 ID”下，粘贴从“API 访问”选项卡复制的 Application Insights ID。********
   
   ![粘贴 Application Insights ID](./media/annotations/4-paste-app-id.png)
   
1. 返回 Application Insights 的“API 访问”窗口，选择“创建 API 密钥”。******** 
   
   ![在“API 访问”选项卡中，选择“创建 API 密钥”。](./media/annotations/5-create-api-key.png)
   
1. 在“创建 API 密钥”窗口中键入说明，选择“编写批注”，然后选择“生成密钥”。************ 复制新密钥。
   
   ![在“创建 API 密钥”窗口中键入说明，选择“编写批注”，然后选择“生成密钥”。](./media/annotations/6-create-api-key.png)
   
1. 在“发布模板”窗口中的“变量”选项卡上，选择“添加”以创建新 API 密钥的变量定义。********

1. 在“名称”下输入 `ApiKey`，然后在“值”下粘贴从“API 访问”选项卡复制的 API 密钥。************
   
   ![在“Azure DevOps 变量”选项卡中选择“添加”，将变量命名为 ApiKey，然后在“值”下面粘贴 API 密钥。](./media/annotations/7-paste-api-key.png)
   
1. 在“发布模板”主窗口中选择“保存”以保存模板。****

## <a name="view-annotations"></a>查看批注


   > [!NOTE]
   > 版本批注当前在 Application Insights 的 "度量值" 窗格中不可用

现在，每当使用发布模板部署新版本时，就会将批注发送到 Application Insights。 可在以下位置查看批注：

使用情况窗格，还可以手动创建版本批注：

![显示一段时间内显示的用户访问数的条形图的屏幕截图。 版本批注在图表上方显示为绿色复选标记，指示发布发生的时间](./media/annotations/usage-pane.png)

在任何基于日志的工作簿查询中，可视化效果沿 x 轴显示时间。

![工作簿窗格的屏幕截图，其中显示了带批注的时序记录查询](./media/annotations/workbooks-annotations.png)

若要在工作簿中启用批注，请单击 "**高级设置**"，然后选择 "**显示批注**"。

!["高级设置" 菜单的屏幕截图，其中的单词显示批注突出显示批注，设置旁边有一个选中标记，以启用它。](./media/annotations/workbook-show-annotations.png)

选择任何批注标记以打开有关版本的详细信息，包括请求者、源代码管理分支、发布管道和环境。

## <a name="create-custom-annotations-from-powershell"></a>通过 PowerShell 创建自定义批注
可以使用 GitHub 中的 [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell 脚本，通过所需的任何流程创建批注，而无需使用 Azure DevOps。 

1. 创建 [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) 的本地副本。
   
1. 使用上述过程中的步骤获取 Application Insights ID，并通过 Application Insights 的“API 访问”选项卡创建 API 密钥。****
   
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
