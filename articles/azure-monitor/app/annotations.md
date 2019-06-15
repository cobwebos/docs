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
ms.date: 05/31/2019
ms.author: mbullwin
ms.openlocfilehash: 6567d7f2ebaab5bd7b5bc8fb7b5a62970f169161
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476168"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>为 Application Insights 中的指标图表添加批注

[指标资源管理器](../../azure-monitor/app/metrics-explorer.md)图表上的批注显示将新版本部署到了何处，或者显示其他重要事件。 使用批注可让轻松查看更改是否对应用程序的性能产生了任何影响。 [Azure DevOps Services 生成系统](https://docs.microsoft.com/azure/devops/pipelines/tasks/)可自动创建批注。 也可以通过 PowerShell 创建批注，用于标记所要处理的任何事件。

> [!NOTE]
> 本文反映了已弃用的**经典指标体验**。 批注目前仅在经典体验和 **[工作簿](../../azure-monitor/app/usage-workbooks.md)** 中可用。 若要详细了解当前指标体验，可查阅[此文](../../azure-monitor/platform/metrics-charts.md)。

![批注的示例](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-devops-services-build"></a>使用 Azure DevOps Services 生成系统创建版本批注

版本批注是 Azure DevOps Services 的基于云的 Azure Pipelines 服务功能。

### <a name="install-the-annotations-extension-one-time"></a>安装批注扩展（一次性操作）
若要创建版本批注，必须安装 Visual Studio Marketplace 中提供的多个 Azure DevOps Services 扩展中的一个。

1. 登录到 [Azure DevOps Services](https://azure.microsoft.com/services/devops/) 项目。
2. 在 Visual Studio Marketplace 中，[获取“版本批注”扩展](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)，并将其添加到 Azure DevOps Services 组织。

![选择 Azure DevOps 的组织，然后安装。](./media/annotations/1-install.png)

只需针对 Azure DevOps Services 组织执行此操作一次。 现在，可为组织中的任何项目配置版本批注。

### <a name="configure-release-annotations"></a>配置版本批注

需要为每个 Azure DevOps Services 发布模板单独获取一个 API 密钥。

1. 登录到[Microsoft Azure 门户](https://portal.azure.com)并打开监视你的应用程序的 Application Insights 资源。 （如果尚未创建此资源，可以[立即创建一个](../../azure-monitor/app/app-insights-overview.md)。）
2. 打开**API 访问权限**选项卡并复制**Application Insights ID**。
   
    ![在 portal.azure.com 中打开你的 Application Insights 资源，并选择“设置”。 打开“API 访问”。 复制应用程序 ID](./media/annotations/2-app-id.png)

4. 在另一个浏览器窗口中，打开（或创建）可从 Azure DevOps Services 管理部署的发布模板。
   
    添加一个任务，并从菜单中选择“Application Insights 版本批注”任务。

   ![单击加号添加任务，然后选择 Application Insights 版本批注。 粘贴 Application Insights id。](./media/annotations/3-add-task.png)

    粘贴**应用程序 ID**从 API 访问权限选项卡中复制。
   
    ![粘贴 Application Insights ID](./media/annotations/4-paste-app-id.png)

5. 返回“Azure”窗口，创建新的 API 密钥并复制它。
   
    ![在 Azure 窗口中的 API 访问权限选项卡上，单击创建 API 密钥。](./media/annotations/5-create-api-key.png)

    ![在创建 API 密钥选项卡中提供的注释、 检查写入批注和单击生成密钥。 复制新密钥。](./media/annotations/6-create-api-key.png)

6. 打开发布模板的“配置”选项卡。
   
    创建 `ApiKey` 的变量定义。
   
    将 API 密钥粘贴到 ApiKey 变量定义。
   
    ![在 Azure DevOps 服务窗口中，选择变量选项卡并单击添加。 设置 ApiKey 并值设置名称，粘贴你生成的密钥并单击锁状图标。](./media/annotations/7-paste-api-key.png)
1. 最后，**保存**发布管道。


## <a name="view-annotations"></a>查看批注
现在，每当使用发布模板部署新版本时，就会将批注发送到 Application Insights。 批注会显示在指标资源管理器中的图表上。

单击任一批注标记 （浅灰色箭头） 以打开详细信息，了解此版本，包括请求者、 源控制分支、 发布管道、 环境和的详细信息。

![单击任一版本批注标记。](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>通过 PowerShell 创建自定义批注
也可以通过所偏好的任何过程（不使用 Azure DevOps Services）创建批注。 


1. 为 [GitHub 中的 Powershell 脚本](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)创建一个本地副本。

2. 获取应用程序 ID 并从 API 访问权限选项卡创建 API 密钥。

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

* [创建工作项](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [使用 PowerShell 自动化](../../azure-monitor/app/powershell.md)
