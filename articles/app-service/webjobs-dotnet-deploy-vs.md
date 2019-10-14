---
title: 使用 Visual Studio 开发和部署 WebJobs - Azure
description: 了解如何使用 Visual Studio 开发 Azure WebJobs 并将其部署到 Azure 应用服务。
author: ggailey777
manager: gwallace
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: ac458b01135be8628fbf939e310f8bda02b8d290
ms.sourcegitcommit: 9858ab651a520c26f0ed18215e650efbf1fc5de9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2019
ms.locfileid: "72303536"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>使用 Visual Studio 开发和部署 WebJobs - Azure 应用服务

本文介绍如何使用 Visual Studio 将控制台应用程序项目作为 [Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226) 部署到[应用服务](overview.md)中的 Web 应用。 有关如何使用 [Azure 门户](https://portal.azure.com)部署 WebJobs 的信息，请参阅[使用 WebJobs 运行后台任务](webjobs-create.md)。

可将多个 WebJob 发布到单个 Web 应用。 请确保 Web 应用中的每个 WebJob 具有唯一的名称。

[Azure WebJobs SDK](webjobs-sdk-how-to.md) 版本 3.x 可用于开发作为 .NET Core 应用或 .NET Framework 应用运行的 WebJobs，而版本 2.x 仅支持 .NET Framework。 部署 WebJobs 项目的方式因该项目是 .NET Core 项目还是 .NET Framework 项目而有所不同。

## <a name="webjobs-as-net-core-console-apps"></a>用作 .NET Core 控制台应用的 WebJob

使用 WebJobs 版本 3.x 时，可以创建并发布用作 .NET Core 控制台应用的 WebJob。 有关创建 .NET Core 控制台应用程序并将其作为 WebJob 发布到 Azure 的分步说明，请参阅[用于事件驱动式后台处理的 Azure WebJobs SDK 入门](webjobs-sdk-get-started.md)。

> [!NOTE]
> .NET Core WebJob 无法与 Web 项目相链接。 如果需要将 WebJob 与 Web 应用一起部署，应该[创建用作 .NET Framework 控制台应用的 WebJob](#webjobs-as-net-framework-console-apps)。  

### <a name="deploy-to-azure-app-service"></a>部署到 Azure 应用服务

通过 Visual Studio 将 .NET Core WebJob 发布到应用服务所用的工具，与发布 ASP.NET Core 应用所用的工具相同。

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Web 作业类型

默认情况下，从 .NET Core 控制台项目发布的 WebJob 只会在触发后才会运行，或者按需运行。 也可以将项目更新为[按计划运行](#scheduled-execution)或连续运行。

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>按计划执行

将 .NET Core 控制台应用程序发布到 Azure 时，会将一个新的 *settings.job* 文件添加到项目。 使用此文件可以设置 WebJob 的执行计划。 有关详细信息，请参阅[计划触发的 WebJob](#scheduling-a-triggered-webjob)。

#### <a name="continuous-execution"></a>连续执行

可以使用 Visual Studio 来更改 WebJob，以便在 Azure 中启用 Always On 后连续运行该 WebJob。

1. [将项目发布到 Azure](#deploy-to-azure-app-service)（如果尚未这样做）。

1. **在“解决方案资源管理器”** 中，右键单击该项目并选择“发布”。

1. 在“发布”选项卡中选择“设置”。 

1. 在“配置文件设置”对话框中，为“WebJob 类型”选择“连续”，然后选择“保存”。

    ![WebJob 的“发布设置”对话框](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. 选择“发布”，以使用更新的设置重新发布 WebJob。

## <a name="webjobs-as-net-framework-console-apps"></a>用作 .NET Framework 控制台应用的 WebJob  

当 Visual Studio 部署启用 WebJobs 的 .NET Framework 控制台应用程序项目时，它会将运行时文件复制到 Web 应用中的相应文件夹（对于连续运行的 WebJobs，该文件夹为 *App_Data/jobs/continuous*，对于按计划运行或按需运行的 WebJobs，该文件夹为 *App_Data/jobs/triggered*）。

已启用 Web 作业的项目中添加了以下项：

* [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 包。
* [webjob-publish-settings.json](#publishsettings) 文件，其中包含部署和计划程序设置。 

![显示在控制台应用中添加哪些项目才能让部署作为 Web 作业的插图](./media/webjobs-dotnet-deploy-vs/convert.png)

可以将这些项添加到现有控制台应用程序项目，或使用模板创建已启用 Web 作业的新控制台应用程序项目。 

以 Web 作业的方式部署项目，或者将项目链接到 Web 项目，因此每要部署 Web 项目时，项目便会自动部署。 为了链接项目，Visual Studio 会在 Web 项目的 [webjobs-list.json](#webjobslist) 文件中包含启用 Web 作业的项目的名称。

![显示链接到 Web 项目的 Web 作业项目的插图](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>先决条件

如果使用的是 Visual Studio 2015，请安装[用于 .NET 的 Azure SDK (Visual Studio 2015)](https://azure.microsoft.com/downloads/)。

如果使用的是 Visual Studio 2017，请安装 [Azure 开发工作负荷](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads)。

### <a id="convert"></a> 为现有控制台应用程序项目启用 WebJobs 部署

可以使用两个选项：

* [使用 Web 项目启用自动部署](#convertlink)。

  配置现有控制台应用程序项目，以便在部署 Web 项目时，它以 Web 作业的方式自动部署。 要在运行相关 Web 应用程序的同一 Web 应用中运行 Web 作业时，请使用此选项。

* [不使用 Web 项目启用部署](#convertnolink)。

  配置现有控制台应用程序项目以 Web 作业的方式部署，且不提供 Web 项目的链接。 要在 Web 应用中运行 Web 作业，且 Web 应用中没有正在运行的 Web 应用程序时，请使用此选项。 为了能够缩放不受 Web 应用程序资源影响的 Web 作业资源，可能要执行此操作。

#### <a id="convertlink"></a> 使用 Web 项目启用自动 Web 作业部署

1. 右键单击“解决方案资源管理器”中的 Web 项目，并依次单击“添加” > “用作 Azure Web 作业的现有项目”。
   
    ![用作 Azure Web 作业的现有项目](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    此时显示[添加 Azure Web 作业](#configure)对话框。
2. 在“项目名称”下拉列表中，选择要添加为 Web 作业的控制台应用程序项目。
   
    ![在“添加 Azure Web 作业”对话框中选择项目](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. 完成[添加 Azure Web 作业](#configure)对话框，并单击“确定”。 

#### <a id="convertnolink"></a> 不使用 Web 项目启用 Web 作业部署
1. 右键单击“解决方案资源管理器”中的控制台应用程序项目，然后单击“发布为 Azure Web 作业”。 
   
    ![发布为 Azure Web 作业](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    此时显示[添加 Azure Web 作业](#configure)对话框，其“项目名称”框中已选中该项目。
2. 完成[添加 Azure Web 作业](#configure)对话框，并单击“确定”。
   
   此时显示“发布 Web”向导。  如果不打算立即发布，请关闭向导。 输入的设置会保存，以便在[部署项目](#deploy)时使用。

### <a id="create"></a>创建已启用 Web 作业的新项目
若要创建已启用 Web 作业的新项目，可以使用控制台应用程序项目模板，并根据 [上一节](#convert)所述启用 Web 作业部署。 或者，可以使用 Web 作业新建项目模板：

* [为独立的 Web 作业使用 Web 作业新建项目模板](#createnolink)
  
    创建一个项目，并将它配置为以 Web 作业的方式部署，且不提供 Web 项目的链接。 要在 Web 应用中运行 Web 作业，且 Web 应用中没有正在运行的 Web 应用程序时，请使用此选项。 为了能够缩放不受 Web 应用程序资源影响的 Web 作业资源，可能要执行此操作。
* [在链接到 Web 项目的 Web 作业中使用 Web 作业新建项目模板](#createlink)
  
    创建一个项目，该项目配置为在针对位于相同解决方案中的 Web 项目进行部署时，自动以 Web 作业的方式部署。 要在运行相关 Web 应用程序的同一 Web 应用中运行 Web 作业时，请使用此选项。

> [!NOTE]
> Web 作业新建项目模板会自动安装 NuGet 包，并在 *Program.cs* 中包含适用于 [WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs) 的代码。 如果不想使用 WebJobs SDK，请删除或更改 Program.cs 中的 `host.RunAndBlock` 语句。
> 
> 

#### <a id="createnolink"></a> 为独立的 Web 作业使用 Web 作业新建项目模板
1. 依次单击“文件” > “新建项目”，然后在“新建项目”对话框中，依次单击“云” > “Azure Web 作业 (.NET Framework)”。
   
    ![显示了 Web 作业模板的“新建项目”对话框](./media/webjobs-dotnet-deploy-vs/np.png)
2. 按照前述说明， [将控制台应用程序项目设为独立的 Web 作业项目](#convertnolink)。

#### <a id="createlink"></a> 在链接到 Web 项目的 Web 作业中使用 Web 作业新建项目模板
1. 右键单击“解决方案资源管理器”中的 Web 项目，并依次单击“添加” > “新建 Azure Web 作业项目”。
   
    ![“新建 Azure Web 作业项目”菜单项](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    此时显示[添加 Azure Web 作业](#configure)对话框。
2. 完成[“添加 Azure Web 作业”](#configure)对话框，并单击“确定”。

### <a id="configure"></a>添加 Azure Web 作业对话框
可在“添加 Azure Web 作业”对话框中输入 Web 作业的名称和 Web 作业的运行模式设置。 

![“添加 Azure Web 作业”对话框](./media/webjobs-dotnet-deploy-vs/aaw2.png)

此对话框中的字段对应于 Azure 门户的“添加 WebJob”对话框中的字段。 有关详细信息，请参阅[使用 Web 作业运行后台任务](webjobs-create.md)。

> [!NOTE]
> * 有关命令行部署的信息，请参阅[启用 Azure Web 作业的命令行或连续传送](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)。
> * 如果部署了某个 WebJob，但后来想要更改该 WebJob 的类型并重新部署，则需要删除 *webjobs-publish-settings.json* 文件。 这样，Visual Studio 就会再次显示发布选项，使你可以更改 Web 作业的类型。
> * 如果部署了某个 Web 作业，并随后将运行模式从连续更改为非连续（或相反），则在重新部署时，Visual Studio 将在 Azure 中创建新的 Web 作业。 如果更改了其他计划设置但保持运行模式不变，或在计划模式与按需模式之间切换，则 Visual Studio 会更新现有的作业，而不是创建新的作业。
> 
> 

### <a id="publishsettings"></a>webjob-publish-settings.json
设置 Web 作业部署的控制台应用程序时，Visual Studio 会安装 [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 包，并将计划信息存储在 Web 作业项目的项目 *Properties* 文件夹中的 *webjob-publish-settings.json* 文件内。 以下是该文件的示例：

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

可以编辑此文件目录，Visual Studio 会提供 IntelliSense。 在 [https://schemastore.org](https://schemastore.org/schemas/json/webjob-publish-settings.json) 中可查看存储的文件架构。  

### <a id="webjobslist"></a>webjobs-list.json
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

可以编辑此文件目录，Visual Studio 会提供 IntelliSense。 在 [https://schemastore.org](https://schemastore.org/schemas/json/webjobs-list.json) 中可查看存储的文件架构。

### <a id="deploy"></a>部署 Web 作业项目
已链接到 Web 项目的 Web 作业项目会通过 Web 项目自动部署。 有关 Web 项目部署的信息，请参阅左侧导航区域中的“操作指南” > “部署应用”。

若要自动部署某个 Web 作业项目，请在“解决方案资源管理器”中右键单击该项目，然后单击“发布为 Azure Web 作业”。 

![发布为 Azure Web 作业](./media/webjobs-dotnet-deploy-vs/paw.png)

对于独立的 Web 作业，会显示 Web 项目使用的相同“发布 Web”向导，但其中的可更改设置更少。

## <a name="scheduling-a-triggered-webjob"></a>计划触发的 WebJob

WebJobs 使用 *settings.job* 文件确定某个 WebJob 是否已运行。 使用此文件可以设置 WebJob 的执行计划。 以下示例从上午 9 点到下午 5 点每隔一小时运行：

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

此文件必须位于 WebJobs 文件夹的根目录下，与 WebJob 的脚本（例如 `wwwroot\app_data\jobs\triggered\{job name}` 或 `wwwroot\app_data\jobs\continuous\{job name}`）放在一起。 从 Visual Studio 部署 Web 作业时，请将 `settings.job` 文件属性标记为“如果较新则复制”。 

[从 Azure 门户创建 WebJob](webjobs-create.md) 时，系统会自动创建 settings.job 文件。

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>CRON 表达式

WebJobs 使用的 CRON 计划表达式与 Azure Functions 中的计时器触发器相同。 若要详细了解 CRON 支持，请参阅[计时器触发器参考文章](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)。

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingjob-reference"></a>setting.job 参考

WebJobs 支持以下设置：

| **设置** | 类型  | **说明** |
| ----------- | --------- | --------------- |
| `is_in_place` | 全部 | 允许作业在原地运行，而无需首先将其复制到临时文件夹。 有关详细信息，请参阅 [WebJobs 工作目录](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory)。 |
| `is_singleton` | 连续 | 仅在横向扩展的单个实例上运行 WebJob。有关详细信息，请参阅[将连续作业设为单一实例](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton)。 |
| `schedule` | 触发 | 根据基于 CRON 的计划运行 WebJob。 有关详细信息，请参阅[计时器触发器参考文章](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)。 |
| `stopping_wait_time`| 全部 | 允许控制关闭行为。 有关详细信息，请参阅[正常关闭](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown)。 |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 WebJobs SDK](webjobs-sdk-how-to.md)
