---
title: "使用 Visual Studio 开发和部署 WebJobs - Azure"
description: "了解如何使用 Visual Studio 开发 Azure WebJobs 并将其部署到 Azure 应用服务。"
services: app-service
documentationcenter: 
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: glenga
ms.openlocfilehash: 59a453aab82d2f78bcc04774e26c8a557b8dbb26
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>使用 Visual Studio 开发和部署 WebJobs - Azure 应用服务

## <a name="overview"></a>概述

本主题介绍如何使用 Visual Studio 将控制台应用程序项目作为 [Azure Web 作业](http://go.microsoft.com/fwlink/?LinkId=390226)部署到[应用服务](app-service-web-overview.md)中的 Web 应用。 有关如何使用 [Azure 门户](https://portal.azure.com)部署 WebJobs 的信息，请参阅[使用 WebJobs 运行后台任务](web-sites-create-web-jobs.md)。

当 Visual Studio 部署启用 Web 作业的控制台应用程序项目时，它会执行两个任务：

* 将运行时文件复制到 Web 应用中的相应文件夹（对于连续 Web 作业，该文件夹为 *App_Data/jobs/continuous*，对于计划的按需 Web 作业，则为 *App_Data/jobs/triggered*）。
* 为已计划在特定时间运行的 Web 作业设置 [Azure 计划程序作业](#scheduler)。 （无需为连续 Web 作业执行此操作。）

已启用 Web 作业的项目中添加了以下项：

* [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 包。
* [webjob-publish-settings.json](#publishsettings) 文件，其中包含部署和计划程序设置。 

![显示在控制台应用中添加哪些项目才能让部署作为 Web 作业的插图](./media/websites-dotnet-deploy-webjobs/convert.png)

可以将这些项添加到现有控制台应用程序项目，或使用模板创建已启用 Web 作业的新控制台应用程序项目。 

以 Web 作业的方式部署项目，或者将项目链接到 Web 项目，因此每要部署 Web 项目时，项目便会自动部署。 为了链接项目，Visual Studio 会在 Web 项目的 [webjobs-list.json](#webjobslist) 文件中包含启用 Web 作业的项目的名称。

![显示链接到 Web 项目的 Web 作业项目的插图](./media/websites-dotnet-deploy-webjobs/link.png)

## <a name="prerequisites"></a>先决条件

如果使用的是 Visual Studio 2015，请安装[用于 .NET 的 Azure SDK (Visual Studio 2015)](https://azure.microsoft.com/downloads/)。

如果使用的是 Visual Studio 2017，请安装 [Azure 开发工作负荷](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads)。

## <a id="convert"></a> 为现有控制台应用程序项目启用 WebJobs 部署

可以使用两个选项：

* [使用 Web 项目启用自动部署](#convertlink)。

  配置现有控制台应用程序项目，以便在部署 Web 项目时，它以 Web 作业的方式自动部署。 要在运行相关 Web 应用程序的同一 Web 应用中运行 Web 作业时，请使用此选项。

* [不使用 Web 项目启用部署](#convertnolink)。

  配置现有控制台应用程序项目以 Web 作业的方式部署，且不提供 Web 项目的链接。 要在 Web 应用中运行 Web 作业，且 Web 应用中没有正在运行的 Web 应用程序时，请使用此选项。 为了能够缩放不受 Web 应用程序资源影响的 Web 作业资源，可能要执行此操作。

### <a id="convertlink"></a>使用 Web 项目启用自动 WebJobs 部署

1. 右键单击“解决方案资源管理器”中的 Web 项目，并依次单击“添加” > “用作 Azure Web 作业的现有项目”。
   
    ![用作 Azure Web 作业的现有项目](./media/websites-dotnet-deploy-webjobs/eawj.png)
   
    此时会显示[“添加 Azure Web 作业”](#configure)对话框。
2. 在“项目名称”下拉列表中，选择要添加为 Web 作业的控制台应用程序项目。
   
    ![在“添加 Azure Web 作业”对话框中选择项目](./media/websites-dotnet-deploy-webjobs/aaw1.png)
3. 完成[“添加 Azure Web 作业”](#configure)对话框，并单击“确定”。 

### <a id="convertnolink"></a> 不使用 Web 项目启用 WebJobs 部署
1. 右键单击“解决方案资源管理器”中的控制台应用程序项目，然后单击“发布为 Azure Web 作业...”。 
   
    ![发布为 Azure Web 作业](./media/websites-dotnet-deploy-webjobs/paw.png)
   
    此时会显示[“添加 Azure Web 作业”](#configure)对话框，其“项目名称”框中已选中该项目。
2. 完成[“添加 Azure Web 作业”](#configure)对话框，并单击“确定”。
   
   此时会显示“发布 Web”向导。  如果不想立即发布，请关闭向导。 输入的设置会保存，以便在[部署项目](#deploy)时使用。

## <a id="create"></a>创建已启用 WebJobs 的新项目
若要创建已启用 Web 作业的新项目，可以使用控制台应用程序项目模板，并根据[上一节](#convert)所述启用 Web 作业部署。 或者，可以使用 Web 作业新建项目模板：

* [为独立的 Web 作业使用 Web 作业新建项目模板](#createnolink)
  
    创建一个项目，并将它配置为以 Web 作业的方式部署，且不提供 Web 项目的链接。 要在 Web 应用中运行 Web 作业，且 Web 应用中没有正在运行的 Web 应用程序时，请使用此选项。 为了能够缩放不受 Web 应用程序资源影响的 Web 作业资源，可能要执行此操作。
* [在链接到 Web 项目的 Web 作业中使用 Web 作业新建项目模板](#createlink)
  
    创建一个项目，该项目配置为在针对位于相同解决方案中的 Web 项目进行部署时，自动以 Web 作业的方式部署。 要在运行相关 Web 应用程序的同一 Web 应用中运行 Web 作业时，请使用此选项。

> [!NOTE]
> Web 作业新建项目模板会自动安装 NuGet 包，并在 *Program.cs* 中包含适用于 [WebJobs SDK](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs) 的代码。 如果不希望使用 WebJobs SDK，请删除或更改 *Program.cs* 中的 `host.RunAndBlock` 语句。
> 
> 

### <a id="createnolink"></a> 为独立的 WebJob 使用 WebJobs 新建项目模板
1. 依次单击“文件” > “新建项目”，然后在“新建项目”对话框中，依次单击“云” > “Azure WebJob (.NET Framework)”。
   
    ![显示了 Web 作业模板的“新建项目”对话框](./media/websites-dotnet-deploy-webjobs/np.png)
2. 按照前述说明，[将控制台应用程序项目设为独立的 Web 作业项目](#convertnolink)。

### <a id="createlink"></a> 为链接到 Web 项目的 WebJob 使用 WebJobs 新建项目模板
1. 右键单击“解决方案资源管理器”中的 Web 项目，并依次单击“添加” > “新建 Azure Web 作业项目”。
   
    ![“新建 Azure Web 作业项目”菜单项](./media/websites-dotnet-deploy-webjobs/nawj.png)
   
    此时会显示[“添加 Azure Web 作业”](#configure)对话框。
2. 完成[“添加 Azure Web 作业”](#configure)对话框，并单击“确定”。

## <a id="configure"></a>“添加 Azure WebJob”对话框
使用“添加 Azure WebJob”对话框可以输入你的 WebJob 的 WebJob 名称和运行模式。 

![“添加 Azure Web 作业”对话框](./media/websites-dotnet-deploy-webjobs/aaw2.png)

此对话框中的字段对应于 Azure 门户的“添加 WebJob”对话框中的字段。 有关详细信息，请参阅[使用 Web 作业运行后台任务](web-sites-create-web-jobs.md)。

> [!NOTE]
> * 有关命令行部署的信息，请参阅[启用 Azure Web 作业的命令行或连续传送](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)。
> * 如果部署了某个 WebJob，但后来想要更改该 WebJob 的类型并重新部署，则需要删除 *webjobs-publish-settings.json* 文件。 这样，Visual Studio 就会再次显示发布选项，使你可以更改 WebJob 的类型。
> * 如果部署了某个 Web 作业，并随后将运行模式从连续更改为非连续（或相反），则在重新部署时，Visual Studio 会在 Azure 中创建新的 Web 作业。 如果更改了其他计划设置但保持运行模式不变，或在计划模式与按需模式之间切换，则 Visual Studio 会更新现有的作业，而不是创建新的作业。
> 
> 

## <a id="publishsettings"></a>webjob-publish-settings.json
设置 Web 作业部署的控制台应用程序时，Visual Studio 会安装 [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 包，并将计划信息存储在 Web 作业项目的项目 *Properties* 文件夹中的 *webjob-publish-settings.json* 文件内。 以下是该文件的示例：

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

可以编辑此文件目录，Visual Studio 将提供 IntelliSense。 在 [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) 中可查看存储的文件架构。  

## <a id="webjobslist"></a>webjobs-list.json
如果将已启用 Web 作业的项目链接到 Web 项目，Visual Studio 会将 Web 作业项目的名称存储在 Web 项目 *Properties* 文件夹的 *webjobs-list.json* 文件中。 该列表可能包含多个 Web 作业项目，如以下示例所示：

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

可以编辑此文件目录，Visual Studio 将提供 IntelliSense。 在 [http://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) 中可查看存储的文件架构。

## <a id="deploy"></a>部署 WebJobs 项目
已链接到 Web 项目的 Web 作业项目会通过 Web 项目自动部署。 有关 Web 项目部署的信息，请参阅左侧导航区域中的“操作指南” > “部署应用”。

若要自动部署某个 Web 作业项目，请在“解决方案资源管理器”中右键单击该项目，然后单击“发布为 Azure Web 作业...”。 

![发布为 Azure Web 作业](./media/websites-dotnet-deploy-webjobs/paw.png)

对于独立的 Web 作业，会显示 Web 项目使用的相同“发布 Web”向导，但其中的可更改设置更少。
