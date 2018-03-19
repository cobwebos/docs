---
title: "使用 Application Insights Profiler 探查 ASP.NET Core Azure Linux Web 应用 | Microsoft Docs"
description: "概念概述以及有关如何启用该工具的分步教程"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 7f3a0a1a22e14f12b86474dd5b1985029074444e
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>使用 Application Insights Profiler 探查 ASP.NET Core Azure Linux Web 应用

此功能目前为预览版

使用 [Application Insights](app-insights-overview.md) 确定实时 Web 应用程序中的每个方法花费了多长时间。 该探查器现在可用于应用服务上的 Linux 中托管的 ASP.NET Core Web 应用。 本指南提供了有关如何为 ASP.NET Core Linux Web 应用收集探查器跟踪的分步说明。

完成此演练后，你的应用将收集类似于下面的屏幕截图的探查器跟踪。 在此示例中，探查器跟踪指明某个特定的 Web 请求较慢，因为大多数时间都花在等待上。 代码中拖慢了应用的热路径前面带有火焰图标。 此示例表明 `HomeController` 中的 `About` 方法拖慢了 Web 应用，因为它正在调用 `Thread.Sleep`。

![探查器跟踪](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="pre-requisites"></a>先决条件
以下说明适用于所有 Windows、Linux 和 Mac 开发环境：

* 安装 [.NET Core SDK 2.1.2 或更高版本](https://www.microsoft.com/net/download/windows/build)
* 根据[入门 - 安装 Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) 中的说明安装 Git

## <a name="setup-project-locally"></a>在本地设置项目

1. 在计算机上打开命令提示符。 以下说明适用于所有 Windows、Linux 和 Mac 开发环境。

2. 创建 ASP.NET Core MVC Web 应用程序
    ```
    dotnet new mvc -n LinuxProfilerTest
    ```
3. 将命令提示符中的目录更改到项目根文件夹

4. 添加用于收集探查器跟踪的 Nuget 包。
    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```
5. 在 HomeController.cs 中添加用于随机延迟几秒钟的一行代码

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```
6. 保存并提交对本地存储库的更改

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-azure-app-service-for-hosting-your-project"></a>创建用于托管你的项目的 Azure 应用服务
1. 创建应用服务 Linux 环境

    ![创建 Linux 应用服务](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. 创建部署凭据。 记下密码，稍后在部署应用时将需要此密码。

    ![创建部署凭据](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. 选择部署选项。 按照 Azure 门户中的说明在 Web 应用中设置本地 Git 存储库。 系统将自动创建一个 Git 存储库。

    ![设置 Git 存储库](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

[此处](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type)有更多部署选项

## <a name="deploy-your-project"></a>部署项目

1. 在命令提示符下，导航到你的项目根文件夹。 添加 Git 远程存储库以指向应用服务上的一个服务：

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```
    * 使用“创建部署凭据”步骤中的“用户名”。
    * 使用“创建应用服务”步骤中的“应用名称”。

2. 通过将更改推送到 Azure 来部署项目

    ```
    git push azure master
    ```
你将看到与下面类似的输出：

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>添加 Application Insights 来监视 Web 应用
1. [创建 Application Insights 资源](./app-insights-create-new-resource.md)
2. 复制 Application Insights 资源的 iKey 并在应用服务中设置以下设置

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![设置应用设置](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    更改应用设置将自动重新启动站点。 应用新设置后，探查器将立即开始运行 2 分钟。 然后，它将每隔一小时运行 2 分钟。

3. 生成发往你的网站的一些流量。 可以多次刷新站点 ```About``` 页面。

4. 等待 2-5 分钟，以便可以将事件聚合到 Application Insights。

5. 在 Azure 门户中导航到 Application Insights 性能窗格。 你会在右下角看到可用的探查器跟踪。

    ![查看跟踪](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="report-issues-to-project-github-repository"></a>将问题报告到项目 Github 存储库
如果你有任何问题或建议，请报告到我们的 github 存储库：[ApplicationInsights-Profiler-AspNetCore：问题](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues)
