---
title: "Application Insights 的版本批注 | Microsoft 文档"
description: "为 Application Insights 中的指标资源管理器图表添加部署或版本标记。"
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: mbullwin
ms.openlocfilehash: a479fa553d64f3820ae8513353484e72b57d30e4
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>为 Application Insights 中的指标图表添加批注
[指标资源管理器](app-insights-metrics-explorer.md)图表上的批注显示将新版本部署到了何处，或者显示其他重要事件。 使用批注可让轻松查看更改是否对应用程序的性能产生了任何影响。 [Visual Studio Team Services 生成系统](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs)可自动创建批注。 也可以[通过 PowerShell 创建批注](#create-annotations-from-powershell)用于标记所要处理的任何事件。

![显示与服务器响应时间的关联的批注示例](./media/app-insights-annotations/00.png)



## <a name="release-annotations-with-vsts-build"></a>使用 VSTS 生成系统创建版本批注

版本批注是 Visual Studio Team Services 的基于云的生成和发布服务的一项功能。 

### <a name="install-the-annotations-extension-one-time"></a>安装批注扩展（一次性操作）
若要创建版本批注，必须安装 Visual Studio 应用商店中提供的多个 Team Service 扩展中的一个。

1. 登录到 [Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online) 项目。
2. 在 Visual Studio 应用商店中，[获取“版本批注”扩展](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)，并将其添加到 Team Services 帐户。

![在 Team Services 网页右上角打开“应用商店”。 选择“Visual Team Services”，并在“生成与发布”下面选择“查看更多”。](./media/app-insights-annotations/10.png)

只需针对 Visual Studio Team Services 帐户执行此操作一次。 现在，可为帐户中的任何项目配置版本批注。 

### <a name="configure-release-annotations"></a>配置版本批注

需要为每个 VSTS 发布模板单独获取一个 API 密钥。

1. 登录到 [Microsoft Azure 门户](https://portal.azure.com)并打开负责监视应用程序的 Application Insights 资源。 （如果尚未创建此资源，可以[立即创建一个](app-insights-overview.md)。）
2. 打开“API 访问”，并复制“Application Insights ID”。
   
    ![在 portal.azure.com 中打开 Application Insights 资源，然后选择“设置”。 打开“API 访问”。 复制应用程序 ID](./media/app-insights-annotations/20.png)

4. 在另一个浏览器窗口中，打开（或创建）可从 Visual Studio Team Services 管理部署的发布模板。 
   
    添加一个任务，并从菜单中选择“Application Insights 版本批注”任务。
   
    粘贴从“API 访问”边栏选项卡复制的**应用程序 ID**。
   
    ![在 Visual Studio Team Services 中打开“发布”，选择一个发布定义，并选择“编辑”。 单击“添加任务”，并选择“Application Insights 版本批注”。 粘贴 Application Insights ID。](./media/app-insights-annotations/30.png)
4. 将“APIKey”字段设置为变量 `$(ApiKey)`。

5. 返回“Azure”窗口，创建新的 API 密钥并复制它。
   
    ![在“Azure”窗口的“API 访问”边栏选项卡中，单击“创建 API 密钥”。 提供注释，选中“写入批注”，并单击“生成密钥”。 复制新密钥。](./media/app-insights-annotations/40.png)

6. 打开发布模板的“配置”选项卡。
   
    创建 `ApiKey` 的变量定义。
   
    将 API 密钥粘贴到 ApiKey 变量定义。
   
    ![在“Team Services”窗口中选择“配置”选项卡，并单击“添加变量”。 设置 ApiKey 的名称并设置“值”，粘贴刚刚生成的密钥，并单击锁状图标。](./media/app-insights-annotations/50.png)
7. 最后，**保存**发布定义。


## <a name="view-annotations"></a>查看批注
现在，每当使用发布模板部署新版本时，就会将批注发送到 Application Insights。 批注会显示在指标资源管理器中的图表上。

单击任一批注标记可打开有关该版本的详细信息，包括请求者、源控制分支、发布定义和环境等。

![单击任一版本批注标记。](./media/app-insights-annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>通过 PowerShell 创建自定义批注
也可以通过所偏好的任何过程（不使用 VS Team System）创建批注。 


1. 为 [GitHub 中的 Powershell 脚本](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)创建一个本地副本。

2. 通过“API 访问”边栏选项卡获取应用程序 ID 并创建 API 密钥。

3. 调用如下所示的脚本：

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

可以轻松修改脚本，例如，为以往的内容创建批注。

## <a name="next-steps"></a>后续步骤

* [创建工作项](app-insights-diagnostic-search.md#create-work-item)
* [使用 PowerShell 自动化](app-insights-powershell.md)
