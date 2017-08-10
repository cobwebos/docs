---
title: "在 Linux Web 应用中使用 .NET Core | Microsoft Docs"
description: "在 Linux Web 应用中使用 .NET Core。"
keywords: "azure 应用服务、web 应用、dotnet、core、linux、oss"
services: app-service
documentationCenter: 
authors: michimune, rachelappel
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: aelnably;wesmc;mikono;rachelap
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: cbcc43e04e58894b6505fdf6c48c7792343049b9
ms.contentlocale: zh-cn
ms.lasthandoff: 07/26/2017

---

# <a name="use-net-core-in-an-azure-web-app-on-linux"></a>在 Linux 上的 Azure Web 应用中使用 .NET Core #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

Linux [Web 应用](https://docs.microsoft.com/azure/app-service-web/app-service-linux-intro)使用 Linux 操作系统，提供高度可缩放的自修补 Web 托管服务。 本教程逐步说明如何在 Linux 上的 Azure Web 应用中创建 [.NET Core](https://docs.microsoft.com/aspnet/core/) 应用。 

![Linux 上的 Web 应用][10]

可以在 Mac、Windows 或 Linux 计算机上执行以下步骤。

## <a name="prerequisites"></a>先决条件 ##

完成本教程： 

* 安装 [.NET Core SDK](https://www.microsoft.com/net/download/core)。
* 安装 [Git](https://git-scm.com/downloads)。

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-local-net-core-application"></a>创建本地 .NET Core 应用程序 ##

启动新的终端会话。 创建一个名为 `hellodotnetcore` 的目录，并将当前目录切换到该目录。 然后键入以下命令： 

```
dotnet new web
``` 

  此命令在当前目录下创建三个文件（*hellodotnetcore.csproj*、*Program.cs* 和 *Startup.cs*）和一个空文件夹 (*wwwroot/*)。 `.csproj` 文件的内容应如下所示： 

```xml
  <!-- Empty lines are omitted. -->

  <Project Sdk="Microsoft.NET.Sdk.Web">
        <PropertyGroup>
        <TargetFramework>netcoreapp1.1</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
        <Folder Include="wwwroot\" />
        </ItemGroup>
        <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore" Version="1.1.2" />
        </ItemGroup>
  </Project>
```

此应用是 Web 应用程序，因此已向 *hellodotnetcore.csproj* 文件自动添加对 ASP.NET Core 包的引用。 包的版本号根据所选框架进行设置。 此示例使用 .NET Core 1.1，因此引用的是 ASP.NET Core 版本 1.1.2。

## <a name="build-and-test-the-application-locally"></a>在本地生成和测试应用程序 ##

可以使用 `dotnet restore` 命令（后跟 `dotnet run` 命令）生成并运行 .NET Core 应用，如下所示：

```
dotnet restore
dotnet run
```


应用程序启动时，会显示一条消息，指示应用正在端口上侦听传入请求。 

```bash
Hosting environment: Production
Content root path: C:\hellodotnetcore
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

使用浏览器浏览到 `http://localhost:5000/` 对其进行测试。 如果一切正常，则显示一条“Hello World!” 的结果文本。

![使用浏览器进行测试][7]

## <a name="create-a-net-core-app-in-the-azure-portal"></a>在 Azure 门户中创建 .NET Core 应用 ##

首先需要创建一个空的 Web 应用。 登录到 [Azure 门户](https://portal.azure.com/)，新建一个 [Linux Web 应用](https://portal.azure.com/#create/Microsoft.AppSvcLinux)。

![创建 Web 应用][1]

当“创建”页打开时，提供有关 Web 应用的详细信息：

![选择 .NET Core 运行时堆栈][2]

参照下表填写“创建”页，然后选择“确定”和“创建”创建应用。

| 设置      | 建议的值  | 说明                                        |
| ------------ | ---------------- | -------------------------------------------------- |
| 应用程序名称 | hellodotnetcore  | 应用的名称。 此名称必须唯一。 |
| 订阅 | 选择现有订阅 | Azure 订阅。 |
| 资源组 | myResourceGroup |  要包含 Web 应用的 Azure 资源组。 |
| 应用服务计划 | 现有应用服务计划的名称 |  应用服务计划。  |
| 配置容器 | .NET Core 1.1 | 此 Web 应用的容器类型：内置、Docker 或专用注册表。 |
| 映像源  | 内置  |  映像的源。 |
| 运行时堆栈  | .NET Core 1.1  | 运行时堆栈和版本。  |

## <a name="deploy-your-application-via-git"></a>通过 Git 部署应用程序 ##

使用 Git 将 .NET Core 应用程序部署到 Linux 上的 Azure App Service Web 应用。

新的 Azure Web 应用已配置了 Git 部署。 可以通过在插入 Web 应用名称后导航到以下 URL 来找到 Git 部署 URL：

```https://{your web app name}.scm.azurewebsites.net/api/scm/info```

Git URL 具有基于 Web 应用名称的以下形式：

```https://{your web app name}.scm.azurewebsites.net/{your web app name}.git```

运行以下命令将本地应用程序部署到 Azure Web 应用： 
 
```bash
git init
git remote add azure <Git deployment URL from above>
git add *.csproj *.cs
git commit -m "Initial deployment commit"
git push azure master
```

在向 Azure 推送应用程序的源文件时，无需推送 *bin/* 或 *obj/* 目录下的任何文件，因为应用程序是在云中生成的。 生成过程完成后，会将二进制文件复制到应用程序的目录 */home/site/wwwroot/* 中。

确认远程部署操作报告了成功消息。 推送操作可能需要一些时间，因为包解析和生成过程在云中运行。 系统会显示多条状态消息，其中一些消息指示已复制文件。 输出应如下所示：

```bash
/* some output has been removed for brevity */
remote: Copying file: 'System.Net.Websockets.dll' 
remote: Copying file: 'System.Runtime.CompilerServices.Unsafe.dll' 
remote: Copying file: 'System.Runtime.Serialization.Primitives.dll' 
remote: Copying file: 'System.Text.Encodings.Web.dll' 
remote: Copying file: 'hellodotnetcore.deps.json' 
remote: Copying file: 'hellodotnetcore.dll' 
remote: Omitting next output lines...
remote: Finished successfully.
remote: Running post deployment commands...
remote: Deployment successful.
To https://hellodotnetcore.scm.azurewebsites.net/
 * [new branch]           master -> master

```

在部署完成后，重新启动 Web 应用以使部署生效。 若要执行此操作，请转到 Azure 门户，并导航到 Web 应用的“概述”页。 选择该页中的“重新启动”按钮。 出现弹出窗口时，选择“是”进行确认。 然后可以浏览 Web 应用，如下所示：

![浏览部署到 Linux 上的 Azure App Service 的 .NET Core 应用][10]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>后续步骤
* [Linux 上的 Azure 应用服务 Web 应用常见问题解答](./app-service-linux-faq.md)

[1]: ./media/app-service-linux-using-dotnetcore/top-level-create.png
[2]: ./media/app-service-linux-using-dotnetcore/dotnet-new-webapp.png
[7]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-local.png
[10]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-azure.png

