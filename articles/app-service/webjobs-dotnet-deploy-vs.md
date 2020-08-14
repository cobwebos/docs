---
title: 使用 Visual Studio 开发和部署 Web 作业
description: 了解如何在 Visual Studio 中开发 Azure WebJobs，以及如何将其部署到 Azure 应用服务，包括创建计划任务。
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: devx-track-csharp, vs-azure
ms.date: 07/30/2020
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 14bb693ccaa1b1d16a1d07b7ee1cdeb4493960f5
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212870"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio"></a>使用 Visual Studio 开发和部署 Web 作业

本文介绍如何使用 Visual Studio 将控制台应用程序项目作为[Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226)部署到[Azure App Service](overview.md)中的 web 应用。 有关如何使用 [Azure 门户](https://portal.azure.com)部署 web 作业的信息，请参阅 [在 Azure App Service 中使用 web 作业运行后台任务](webjobs-create.md)。

您可以选择开发作为 [.Net Core 应用](#webjobs-as-net-core-console-apps) 程序或 [.NET Framework 应用程序](#webjobs-as-net-framework-console-apps)运行的 web 作业。 [Azure WebJobs SDK](webjobs-sdk-how-to.md) 版本 3.x 可用于开发作为 .NET Core 应用或 .NET Framework 应用运行的 WebJobs，而版本 2.x 仅支持 .NET Framework。 对于 .NET Core 项目，部署 Web 作业项目的方式与 .NET Framework 项目不同。

可以将多个 web 作业发布到单个 web 应用，前提是 web 应用中的每个 web 作业都有唯一的名称。

## <a name="webjobs-as-net-core-console-apps"></a>用作 .NET Core 控制台应用的 WebJob

在 Azure WebJobs SDK 的版本1.x 中，可以创建 Web 作业并将其发布为 .NET Core 控制台应用程序。 有关以 Web 作业形式创建 .NET Core 控制台应用并将其发布到 Azure 的分步说明，请参阅 [AZURE WEBJOBS SDK 入门，以进行事件驱动的后台处理](webjobs-sdk-get-started.md)。

> [!NOTE]
> 无法通过 web 项目链接 .NET Core web 作业。 如果需要使用 web 应用部署 WebJob，请 [创建 Web 作业作为 .NET Framework 控制台应用](#webjobs-as-net-framework-console-apps)。  

### <a name="deploy-to-azure-app-service"></a>部署到 Azure 应用服务

若要从 Visual Studio 发布 .NET Core WebJob Azure App Service，请使用与发布 ASP.NET Core 应用相同的工具。

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

## <a name="webjobs-as-net-framework-console-apps"></a>用作 .NET Framework 控制台应用的 WebJob  

如果使用 Visual Studio 来部署启用了 web 作业的 .NET Framework 控制台应用项目，则会将运行时文件复制到 web 应用中的相应文件夹， (*App_Data/jobs/continuous* 用于连续 web 作业，并 *App_Data/jobs/triggered* 用于计划的或按需 web 作业) 。

Visual Studio 将以下项添加到启用了 Web 作业的项目中：

* [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 包。
* [webjob-publish-settings.json](#publishsettings) 文件，其中包含部署和计划程序设置。 

![显示如何将添加到控制台应用以启用部署的关系图](./media/webjobs-dotnet-deploy-vs/convert.png)

可以将这些项添加到现有控制台应用程序项目，也可以使用模板来创建启用了 Web 作业的新控制台应用程序项目。 

以 web 作业的方式部署项目，或者将项目链接到 web 项目，以便在部署 web 项目时自动部署。 为了链接项目，Visual Studio 会在 Web 项目的 [webjobs-list.json](#webjobslist) 文件中包含启用 Web 作业的项目的名称。

![显示链接到 Web 项目的 Web 作业项目的插图](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>先决条件

安装 Visual Studio 2017 或 Visual Studio 2019 和 [Azure 开发工作负荷](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads)。

### <a name="enable-webjobs-deployment-for-an-existing-console-app-project"></a><a id="convert"></a> 为现有的控制台应用程序项目启用 Web 作业部署

可以使用两个选项：

* [使用 Web 项目启用自动部署](#convertlink)。

  配置现有的控制台应用程序项目，以便在部署 web 项目时将其自动部署为 web 作业。 要在运行相关 Web 应用程序的同一 Web 应用中运行 Web 作业时，请使用此选项。

* [不使用 Web 项目启用部署](#convertnolink)。

  将现有控制台应用程序项目配置为单独部署为 WebJob，无需链接到 web 项目。 要在 Web 应用中运行 Web 作业，且 Web 应用中没有正在运行的 Web 应用程序时，请使用此选项。 你可能想要这样做，以便独立于 web 应用程序资源缩放 WebJob 资源。

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> 使用 Web 项目启用自动 Web 作业部署

1. 在**解决方案资源管理器**中右键单击 web 项目，然后选择 "**添加**  >  **现有项目作为 Azure WebJob**"。
   
    ![用作 Azure Web 作业的现有项目](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    此时显示[添加 Azure Web 作业](#configure)对话框。
2. 在 " **项目名称** " 下拉列表中，选择要添加为 web 作业的控制台应用项目。
   
    ![在 "添加 Azure Web 作业" 对话框中选择项目](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. 完成 " [添加 Azure WebJob](#configure) " 对话框，然后选择 **"确定"**。 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> 不使用 Web 项目启用 Web 作业部署
1. 右键单击 " **解决方案资源管理器**中的控制台应用项目，然后选择" **发布为 Azure WebJob**"。 
   
    ![发布为 Azure Web 作业](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    此时显示[添加 Azure Web 作业](#configure)对话框，其“项目名称”框中已选中该项目。
2. 完成 " [添加 Azure WebJob](#configure) " 对话框，然后选择 **"确定"**。
   
   此时显示“发布 Web”向导。 如果不打算立即发布，请关闭向导。 输入的设置会保存，以便在[部署项目](#deploy)时使用。

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>创建已启用 Web 作业的新项目
若要创建已启用 Web 作业的新项目，请使用控制台应用程序项目模板，并按 [上一部分所](#convert)述启用 web 作业部署。 或者，可以使用 Web 作业新建项目模板：

* [为独立的 Web 作业使用 Web 作业新建项目模板](#createnolink)
  
    创建一个项目，并将它配置为以 Web 作业的方式部署，且不提供 Web 项目的链接。 要在 Web 应用中运行 Web 作业，且 Web 应用中没有正在运行的 Web 应用程序时，请使用此选项。 你可能想要这样做，以便独立于 web 应用程序资源缩放 WebJob 资源。
* [在链接到 Web 项目的 Web 作业中使用 Web 作业新建项目模板](#createlink)
  
    在同一解决方案中部署 web 项目时，创建一个配置为自动部署的项目。 要在运行相关 Web 应用程序的同一 Web 应用中运行 Web 作业时，请使用此选项。

> [!NOTE]
> Web 作业新建项目模板会自动安装 NuGet 包，并在 *Program.cs* 中包含适用于 [WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs) 的代码。 如果不想使用 WebJobs SDK，请删除或更改 Program.cs 中的 `host.RunAndBlock` 语句。
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> 为独立的 Web 作业使用 Web 作业新建项目模板
1. 选择“文件” > “新建” > “项目”  。 在 **创建 "新建项目** " 对话框中，搜索并选择 " **Azure WebJob ( .NET Framework ** c #) "。
   
2. 按照前面的说明，将 [控制台应用程序项目设为独立的 Web 作业项目](#convertnolink)。

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> 在链接到 Web 项目的 Web 作业中使用 Web 作业新建项目模板
1. 在**解决方案资源管理器**中右键单击 web 项目，然后选择 "**添加**  >  **新的 Azure WebJob 项目**"。
   
    ![“新建 Azure Web 作业项目”菜单项](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    此时显示[添加 Azure Web 作业](#configure)对话框。
2. 完成 " [添加 Azure WebJob](#configure) " 对话框，然后选择 **"确定"**。


### <a name="webjob-publish-settingsjson-file"></a><a id="publishsettings"></a> 文件webjob-publish-settings.js
当你为 Web 作业部署配置控制台应用时，Visual Studio 将安装[web.config](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/)包，并将计划信息存储在 web 作业项目的 "项目*属性*" 文件夹中的 " *webjob-publish-settings.js"* 文件中。 以下是该文件的示例：

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
  "webJobName": "WebJob1",
  "startTime": "null",
  "endTime": "null",
  "jobRecurrenceFrequency": "null",
  "interval": null,
  "runMode": "Continuous"
}
```

可以编辑此文件目录，Visual Studio 会提供 IntelliSense。 在 [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) 中可查看存储的文件架构。  

### <a name="webjobs-listjson-file"></a><a id="webjobslist"></a> 文件webjobs-list.js
如果将已启用 Web 作业的项目链接到 Web 项目，Visual Studio 会将 Web 作业项目的名称存储在 Web 项目 *Properties* 文件夹的 *webjobs-list.json* 文件中。 该列表可能包含多个 Web 作业项目，如以下示例所示：

```json
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
```

可在 Visual Studio 中通过 IntelliSense 直接编辑此文件。 文件架构存储在中 [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) 。

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>部署 Web 作业项目
已链接到 web 项目的 web 作业项目会自动部署在 web 项目中。 有关 web 项目部署的信息，请**How-to guides**参阅  >  左侧导航栏中的 "操作指南"**部署应用**。

若要单独部署 Web 作业项目，请在 **解决方案资源管理器** 中右键单击该项目，然后选择 " **发布为 Azure WebJob**"。 

![发布为 Azure Web 作业](./media/webjobs-dotnet-deploy-vs/paw.png)

对于独立的 Web 作业，会显示 Web 项目使用的相同“发布 Web”向导，但其中的可更改设置更少。

### <a name="add-azure-webjob-dialog-box"></a><a id="configure"></a>"添加 Azure Web 作业" 对话框
" **添加 Azure Web 作业** " 对话框可让你输入 web 作业的 web 作业名称和运行模式设置。 

!["添加 Azure Web 作业" 对话框](./media/webjobs-dotnet-deploy-vs/aaw2.png)

此对话框中的某些字段对应于 Azure 门户的 " **添加 Web 作业** " 对话框中的字段。 有关详细信息，请参阅 [在 Azure App Service 中通过 Web 作业运行后台任务](webjobs-create.md)。

Web 作业部署信息：

* 有关命令行部署的信息，请参阅[启用 Azure Web 作业的命令行或连续传送](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)。

* 如果你部署了 WebJob，然后决定要更改 Web 作业的类型并重新部署，请删除文件 * 上的webjobs-publish-settings.js* 。 这样做会导致 Visual Studio 重新显示发布选项，因此你可以更改 Web 作业的类型。

* 如果部署了某个 Web 作业，并随后将运行模式从连续更改为非连续（或相反），则在重新部署时，Visual Studio 将在 Azure 中创建新的 Web 作业。 如果更改了其他计划设置，但使运行模式保持相同或在计划和按需之间切换，则 Visual Studio 会更新现有作业而不是创建新作业。

## <a name="webjob-types"></a>Web 作业类型

Web 作业的类型可以是 "已 *触发* " 或 " *连续*"：

- 触发 (默认) ：触发的 WebJob 基于绑定事件、 [计划](#scheduling-a-triggered-webjob)或按需 () 按需手动触发事件开始。 它在运行 web 应用的所有实例上运行，但你可以选择将 WebJob 限制为单个实例。

- 连续：创建 WebJob 后，会立即启动 [连续](#continuous-execution) web 作业。 这种类型的 WebJob 最适用于未绑定或长时间运行的作业。 如果作业确实终止，可将其重启。  

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="scheduling-a-triggered-webjob"></a>计划触发的 WebJob

将控制台应用程序发布到 Azure 时，Visual Studio 会将 Web 作业的类型设置为默认情况下**触发**，并向项目中添加新的*设置。* 对于触发的 WebJob 类型，可以使用此文件来设置 Web 作业的执行计划。

使用 " *设置* " "作业文件" 设置 web 作业的执行计划。 以下示例从上午 9 点到下午 5 点每隔一小时运行：

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

此文件位于 web 作业文件夹的根目录中，其中包含 Web 作业的脚本，例如 `wwwroot\app_data\jobs\triggered\{job name}` 或 `wwwroot\app_data\jobs\continuous\{job name}` 。 从 Visual Studio 部署 WebJob 时，请将 Visual Studio 中的 *作业* 文件属性标记为 " **如果较新则复制**"。

如果 [从 Azure 门户创建 WebJob](webjobs-create.md)，则会为你创建 " *设置" 作业* 文件。

#### <a name="cron-expressions"></a>CRON 表达式

WebJobs 使用的 CRON 计划表达式与 Azure Functions 中的计时器触发器相同。 若要了解有关 CRON 支持的详细信息，请参阅 [Azure Functions 的计时器触发器](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)。

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

#### <a name="settingsjob-reference"></a>settings.job reference

WebJobs 支持以下设置：

| **设置** | **类型**  | **说明** |
| ----------- | --------- | --------------- |
| `is_in_place` | 全部 | 允许 Web 作业就地运行，而无需先将其复制到临时文件夹。 有关详细信息，请参阅 [WebJob 工作目录](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory)。 |
| `is_singleton` | 连续 | 仅当扩展时，才在单个实例上运行 Web 作业。有关详细信息，请参阅 [将连续作业设置为单一实例](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton)。 |
| `schedule` | 触发 | 根据基于 CRON 的计划运行 WebJob。 有关详细信息，请参阅 [NCRONTAB 表达式](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)。 |
| `stopping_wait_time`| 全部 | 允许控制关闭行为。 有关详细信息，请参阅[正常关闭](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown)。 |

### <a name="continuous-execution"></a>连续执行

如果在 Azure 中启用 **Always on** ，则可以使用 Visual Studio 将 web 作业更改为连续运行：

1. [将项目发布到 Azure](#deploy-to-azure-app-service)（如果尚未这样做）。

1. **在“解决方案资源管理器”** 中，右键单击该项目并选择“发布”。

1. 在 " **发布** " 选项卡中，选择 " **编辑**"。 

1. 在 "**配置文件设置**" 对话框中，为**WebJob 类型**选择 "**连续**"，然后选择 "**保存**"。

    ![Web 作业的 "发布设置" 对话框](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. 选择 "**发布**" 选项卡中的 "**发布**"，重新发布具有更新的设置的 web 作业。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 WebJobs SDK](webjobs-sdk-how-to.md)
