---
title: 使用 Application Insights Profiler 探查 ASP.NET Core Azure Linux Web 应用 | Microsoft Docs
description: 有关如何使用 Application Insights Profiler 的概念概述和分步教程。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 5596c4efeba14e9d2bfdadd7ce92bb6b2c9fcbf0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>使用 Application Insights Profiler 探查 ASP.NET Core Azure Linux Web 应用

此功能目前处于预览状态。

使用 [Application Insights](app-insights-overview.md) 确定实时 Web 应用程序中的每个方法花费了多长时间。 Application Insights Profiler 现在可用于 Azure 应用服务上的 Linux 中托管的 ASP.NET Core Web 应用。 本指南提供了有关如何为 ASP.NET Core Linux Web 应用收集探查器跟踪的分步说明。

完成本演练后，你的应用可以收集下图所示的探查器跟踪。 在此示例中，探查器跟踪指明某个特定的 Web 请求较慢，因为时间都花费在等待上。 代码中拖慢了应用的热路径前面带有火焰图标。 **HomeController** 节中的 **About** 方法拖慢了 Web 应用，因为该方法正在调用 **Thread.Sleep** 函数。

![探查器跟踪](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>先决条件
以下说明适用于所有 Windows、Linux 和 Mac 开发环境：

* 安装 [.NET Core SDK 2.1.2 或更高版本](https://www.microsoft.com/net/download/windows/build)。
* 根据[入门 - 安装 Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) 中的说明安装 Git。

## <a name="set-up-the-project-locally"></a>在本地设置项目

1. 在计算机上打开命令提示符窗口。 以下说明适用于所有 Windows、Linux 和 Mac 开发环境。

2. 创建 ASP.NET Core MVC Web 应用程序：

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

3. 将工作目录切换到项目的根文件夹。

4. 添加用于收集探查器跟踪的 NuGet 包：

    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

5. 在 **HomeController.cs** 节中添加用于随机延迟几秒钟的一行代码：

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

6. 保存并提交对本地存储库的更改：

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>创建用于托管项目的 Linux Web 应用

1. 使用 Linux 上的应用服务创建 Web 应用环境：

    ![创建 Linux Web 应用](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. 创建部署凭据：

    > [!NOTE]
    > 记下密码，以便稍后在部署 Web 应用时使用。

    ![创建部署凭据](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. 选择部署选项。 遵照 Azure 门户中的说明在 Web 应用中设置本地 Git 存储库。 系统将自动创建一个 Git 存储库。

    ![设置 Git 存储库](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

有关其他部署选项，请参阅[此文](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type)。

## <a name="deploy-your-project"></a>部署项目

1. 在命令提示符窗口中，浏览到项目的根文件夹。 添加 Git 远程存储库以指向应用服务上的存储库：

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * 使用创建部署凭据时所用的**用户名**。
    * 使用通过 Linux 上的应用服务创建 Web 应用时所用的**应用名称**。

2. 通过将更改推送到 Azure 来部署项目：

    ```
    git push azure master
    ```

应会看到类似于以下示例的输出：

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

1. [创建 Application Insights 资源](./app-insights-create-new-resource.md)。

2. 复制 Application Insights 资源的 **iKey** 并在 Web 应用中指定以下设置：

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![配置应用设置](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    更改应用设置时，站点会自动重启。 应用新设置后，探查器会立即运行两分钟。 然后，探查器每隔一小时运行两分钟。

3. 生成发往你的网站的一些流量。 可以多次刷新站点 **About** 页面来生成流量。

4. 等待 2-5 分钟，以便将事件聚合到 Application Insights。

5. 在 Azure 门户中浏览到 Application Insights 的“性能”窗格。 可以在窗格右下角查看探查器跟踪。

    ![查看探查器跟踪](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>已知问题

### <a name="the-enable-action-in-the-profiler-configuration-pane-doesnt-work"></a>“探查器配置”窗格中的“启用”操作不起作用

> [!NOTE]
> 如果使用 Linux 上的应用服务来托管应用，则不需要在 Application Insights 门户的“性能”窗格中再次启用探查器。 可以在项目中包含 NuGet 包，并在 Web 应用设置中设置 Application Insights **iKey** 值，以启用探查器。

如果遵循 [Application Insights Profiler for Windows](./app-insights-profiler.md) 的启用工作流，并在“配置探查器”窗格中选择“启用”，则会收到错误。 启用操作会尝试在 Linux 环境中安装 Windows 版本的探查器代理。

我们正在努力解决此问题。

![请勿尝试在“性能”窗格中再次启用探查器](./media/app-insights-profiler-aspnetcore-linux/issue-enable-profiler.png)


## <a name="next-steps"></a>后续步骤
如果使用 Azure 应用服务托管的自定义容器，请遵照[为容器化 ASP.NET Core 应用程序启用服务探查器](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp)中的说明启用 Application Insights Profiler。

报告有关 Application Insights GitHub 存储库的任何问题或提出建议：[ApplicationInsights-Profiler-AspNetCore：问题](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues)
