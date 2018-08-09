---
title: Azure WebJobs SDK 入门
description: 用于事件驱动的后台处理的 WebJobs SDK 简介。 了解如何访问 Azure 服务和第三方服务中的数据。
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: glenga
ms.openlocfilehash: 72f7090c285e629149519920ac82f0fe962abc48
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577299"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>用于事件驱动的后台处理的 Azure WebJobs SDK 入门

本文介绍如何创建 Azure WebJobs SDK 项目、在本地运行它，然后将其部署到 Azure 应用服务。

本文中的说明适用于 [Visual Studio 2017](https://www.visualstudio.com/vs/)，也可以使用其他工具（例如 [Visual Studio Code](https://code.visualstudio.com/)）完成相同的任务。

## <a name="what-is-the-azure-webjobs-sdk"></a>什么是 Azure WebJobs SDK

Azure WebJobs SDK 是一种框架，可简化编写后台处理代码来访问 Azure 服务中的数据的任务。 该 SDK 采用声明性语法，以便指定应触发某个函数的事件，例如，在队列中添加了新消息。 触发某个函数后，类似的声明性语法将控制数据的读取和写入。 此触发器和绑定系统会处理大部分与访问 Azure 和第三方服务相关的低级别代码编写任务。

### <a name="functions-triggers-and-bindings"></a>函数、触发器和绑定

WebJobs SDK 项目定义一个或多个函数。 函数是在其方法签名中包含触发器特性的方法。 触发器指定调用函数的条件，绑定指定要读取和写入的数据。 例如，以下函数中的触发器特性告知运行时，每当 `items` 队列中出现队列消息时，就要调用该函数。 `Blob` 特性告知运行时要使用队列消息读取 *workitems* 容器中的 Blob。 `queueTrigger` 参数 &mdash; 中提供的队列消息 &mdash; 的内容是 Blob 名称。

```cs
public static void Run(
    [QueueTrigger("items")] string myQueueItem,
    [Blob("workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="versions-2x-and-3x"></a>版本 2.x 和 3.x

相关说明介绍了如何创建 WebJobs SDK 版本 2.x 项目。 WebJobs SDK 的最新版本是 3.x，但它目前处于预览状态，本文尚未提供该版本的说明。 版本 3.x 中引入的主要更改是使用 .NET Core 而不是 .NET Framework。

### <a name="azure-functions"></a>Azure Functions

[Azure Functions](../azure-functions/functions-overview.md) 基于 WebJobs SDK，在无需直接使用 WebJobs SDK 时可以选择它。 Azure Functions 1.x 使用 WebJobs SDK 2.x。 有关详细信息，请参阅 [Azure Functions 与 WebJobs SDK 之间的比较](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)。

## <a name="prerequisites"></a>先决条件

本文假设你已创建一个 [Azure 帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)，并且对 [Azure 应用服务中的应用](app-service-web-overview.md)有一定的经验。 完成本文中的步骤：

* [安装](https://docs.microsoft.com/visualstudio/install/)包含 **Azure 开发**工作负荷的 Visual Studio 2017。 如果已安装 Visual Studio，但未配置该工作负荷，请选择“工具”>“获取工具和功能”添加该工作负荷。
* [创建应用服务应用](app-service-web-get-started-dotnet-framework.md)。 如果已有一个可在其中部署 WebJob 的应用服务应用，则可以使用该应用，而无需新建。

## <a name="create-a-project"></a>创建一个项目

1. 在 Visual Studio 中，选择“文件”>“新建项目”。

1. 选择“Windows 经典桌面”>“控制台应用(.NET Framework)”。

1. 将项目命名为 *WebJobsSDKSample*，然后选择“确定”。

   ![“新建项目”对话框](./media/webjobs-sdk-get-started/new-project.png)

## <a name="add-webjobs-nuget-package"></a>添加 WebJobs NuGet 包

1. 安装 NuGet 包 `Microsoft.Azure.WebJobs` 的最新稳定 2.x 版本。
 
   下面是版本 2.2.0 的“包管理器控制台”命令：

   ```powershell
   Install-Package Microsoft.Azure.WebJobs -version 2.2.0
   ``` 

## <a name="create-the-jobhost"></a>创建 JobHost

`JobHost` 对象是函数的运行时容器：它侦听触发器并调用函数。 

1. 在 *Program.cs* 中，添加 `using` 语句：

   ```cs
   using Microsoft.Azure.WebJobs;
   ```

1. 将 `Main`方法替换为以下代码：

   ```cs
   static void Main()
   {
       var config = new JobHostConfiguration();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

## <a name="enable-console-logging"></a>启用控制台日志记录

可以使用多个选项在 WebJobs SDK 项目中进行日志记录。 我们建议使用[针对 ASP.NET Core 开发的日志记录框架](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging)。 此框架在存储媒体和筛选方面提供更高的性能和灵活性。 

本部分介绍如何设置使用新框架的控制台日志记录。

1. 安装以下 NuGet 包的最新稳定版本：

   * `Microsoft.Extensions.Logging` - 日志记录框架。
   * `Microsoft.Extensions.Logging.Console` - 控制台提供程序。 提供程序将日志发送到特定的目标（在本例中为控制台）。 
 
   下面是版本 2.0.1 的“包管理器控制台”命令：

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.0.1
   ``` 

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
   ``` 

1. 在 *Program.cs* 中，添加 `using` 语句：

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. 在 `Main` 方法中添加代码，以便在创建 `JobHost` 之前更新 `JobHostConfiguration`：
 
   ```
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   ```

   此代码进行以下更改：

   * 禁用[仪表板日志记录](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs)。 仪表板是一个旧式监视工具，不建议对高吞吐量生产方案使用仪表板日志记录。
   * 使用默认[筛选](webjobs-sdk-how-to.md#log-filtering)添加控制台提供程序。 

   现在，`Main` 方法如下所示：

   ```
   var config = new JobHostConfiguration();
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   var host = new JobHost(config);
   host.RunAndBlock();
   ```
   
## <a name="create-a-function"></a>创建函数

1. 在项目文件夹中创建 *Functions.cs*，并将模板代码替换为以下代码：

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;

   namespace WebJobsSDKSample
   {
       public class Functions
       {
           public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
           {
               logger.LogInformation(message);
           }
       }
   }
   ```

   `QueueTrigger` 特性告知运行时，在名为 `queue` 的 Azure 存储队列中写入新消息时，应调用此函数。 队列消息的内容将提供给 `message` 参数中的方法代码。 在方法的正文中处理触发器数据。 在此示例中，代码只是记录消息。

   `message` 参数不一定要是字符串。 也可以绑定到 JSON 对象、字节数组或 [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) 对象。 [参阅队列触发器用法](../azure-functions/functions-bindings-storage-queue.md#trigger---usage)。 每个绑定类型（例如队列、Blob 或表）具有一组可以绑定到的不同参数类型。

## <a name="create-a-storage-account"></a>创建存储帐户

在本地运行的 Azure 存储模拟器不具备 WebJobs SDK 所需的全部功能。 因此，在本部分，我们应在 Azure 中创建一个存储帐户，并将项目配置为使用该帐户。

1. 打开“服务器资源管理器”并登录到 Azure。 右键单击“Azure”节点，选择“连接到 Microsoft Azure 订阅”。

   ![登录 Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. 在“服务器资源管理器”中的“Azure”节点下，右键单击“存储”并选择“创建存储帐户”。

   ![“创建存储帐户”菜单](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. 在“创建存储帐户”对话框中，输入存储帐户的唯一名称。

1. 选择在其中创建了应用服务应用的同一**区域**或者靠近的区域。

1. 选择**创建**。

   ![创建存储帐户](./media/webjobs-sdk-get-started/create-storage-account.png)

1. 在“服务器资源管理器”中的“存储”节点下，选择新存储帐户。 在“属性”窗口中，选择“连接字符串”值字段右侧的省略号 (**...**)。

   ![“连接字符串”右侧的省略号](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. 复制连接字符串并将此值保存在某处，以方便再次复制。

   ![复制连接字符串](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-for-running-locally"></a>配置存储以便在本地运行

WebJobs SDK 在“应用设置”集合中查找存储连接字符串。 在本地运行时，它会在 *App.config* 文件或环境变量中查找此值。

1. 将以下 XML 添加到 *App.config* 文件中紧靠在 `<configuration>` 左标记的后面。

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

1. 将 *{storage connection string}* 替换为先前复制的连接字符串。

   稍后在 Azure 中配置应用服务应用时，会再次使用该连接字符串。

## <a name="test-locally"></a>本地测试

在本部分，我们将生成并在本地运行项目，然后通过创建队列消息来触发函数。

1. 按 Ctrl+F5 运行项目。

   控制台显示运行时已找到函数，并等待队列消息触发该函数。

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   ```

   可能会看到有关 `ServicePointManager` 设置的警告消息。 针对此项目执行测试时，可以忽略该警告。 有关该警告的详细信息，请参阅[如何使用 WebJobs SDK](webjobs-sdk-how-to.md#jobhost-servicepointmanager-settings)。

1. 关闭控制台窗口。

1. 在“服务器资源管理器”中，展开新存储帐户所在的节点，然后右键单击“队列”。 

1. 选择“创建队列”。 

1. 输入 *queue* 作为队列名称，然后选择“确定”。

   ![创建队列](./media/webjobs-sdk-get-started/create-queue.png)

1. 右键单击新队列所在的节点，然后选择“查看队列”。

1. 选择“添加消息”图标。

   ![创建队列](./media/webjobs-sdk-get-started/create-queue-message.png)

1. 在“添加消息”对话框中，输入 *Hello World!* 作为**消息正文**，然后选择“确定”。

   ![创建队列](./media/webjobs-sdk-get-started/hello-world-text.png)

1. 再次运行该项目。

   由于在 `ProcessQueueMessage` 函数中使用了 `QueueTrigger` 特性，因此 WeJobs SDK 运行时会在启动时侦听队列消息。 它会在名为 *queue* 的队列中查找新队列消息，并调用函数。

   由于[队列轮询指数退让](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm)，运行时最长可能需要花费 2 分钟才能找到消息并调用函数。 以[开发模式](webjobs-sdk-how-to.md#jobhost-development-settings)运行可以缩减此等待时间。

  控制台输出如下所示：

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   info: Function[0]
         Hello World!
   info: Host.Results[0]
         Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   ```

1. 关闭控制台窗口。

## <a name="add-application-insights-logging"></a>添加 Application Insights 日志记录

在 Azure 中运行项目时，无法通过查看控制台输出来监视函数执行。 我们建议的监视解决方案是 [Application Insights](../application-insights/app-insights-overview.md)。 有关详细信息，请参阅[监视 Azure Functions](../azure-functions/functions-monitoring.md)。

在本部分，我们将执行以下任务来设置 Application Insights 日志记录，然后部署到 Azure：

* 请确保已准备好要使用的应用服务应用和 Application Insights 实例。
* 将应用服务应用配置为使用前面创建的 Application Insights 实例和存储帐户。
* 设置项目，以便将日志记录到 Application Insights。

### <a name="create-app-service-app-and-application-insights-instance"></a>创建应用服务应用和 Application Insights 实例

1. 如果没有可用的应用服务应用，请[创建一个](app-service-web-get-started-dotnet-framework.md)。

1. 如果没有可用的 Application Insights 资源，请[创建一个](../application-insights/app-insights-create-new-resource.md)。 将“应用程序类型”设置为“常规”，并跳过“复制检测密钥”后面的部分。

1. 如果已有可用的 Application Insights 资源，请[复制检测密钥](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key)。

### <a name="configure-app-settings"></a>配置应用设置 

1. 在“服务器资源管理器”中，展开“Azure”下面的“应用服务”节点。

1. 展开应用服务应用所在的资源组，然后右键单击应用服务应用。

1. 选择“查看设置”。

1. 在“连接字符串”框中添加以下条目。

   |名称  |连接字符串  |数据库类型|
   |---------|---------|------|
   |AzureWebJobsStorage | {前面复制的存储连接字符串}|“自定义”|
   
1. 如果“应用程序设置”框中没有 Application Insights 检测密钥，请添加前面复制的检测密钥。 （根据应用服务应用的创建方式，该框中可能已包含检测密钥。）

   |名称  |值  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {instrumentation key} |

1. 将 *{instrumentation key}* 替换为所用 Application Insights 资源中的检测密钥。

1. 选择“保存”。

1. 将以下 XML 添加到 *App.config* 文件中紧靠在连接字符串集合的后面。

   ```xml
   <appSettings>
     <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
   </appSettings>
   ```

1. 将 *{instrumentation key}* 替换为所用 Application Insights 资源中的检测密钥。

   将此数据添加到 *App.config* 文件可以在本地运行项目时测试 Application Insights 连接。 

1. 保存所做更改。

### <a name="add-application-insights-logging-provider"></a>添加 Application Insights 日志记录提供程序

1. 为 Application Insights 日志记录提供程序安装 NuGet 包的最新稳定 2.x 版本：`Microsoft.Azure.WebJobs.Logging.ApplicationInsights`。

   下面是版本 2.2.0 的“包管理器控制台”命令：

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. 为 .NET 配置管理器安装 NuGet 包的最新稳定 4.x 版本：`System.Configuration.ConfigurationManager`。

   下面是版本 4.4.1 的“包管理器控制台”命令：

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

1. 打开 *Program.cs* 并为配置管理器添加 `using` 语句：

   ```csharp
   using System.Configuration;
   ```

1. 将 `Main`方法中的代码替换为以下代码：

   ```csharp
   using (var loggerFactory = new LoggerFactory())
   {
       var config = new JobHostConfiguration();
       var instrumentationKey =
           ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];
       config.DashboardConnectionString = "";
       config.LoggerFactory = loggerFactory
           .AddApplicationInsights(instrumentationKey, null)
           .AddConsole();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

   此代码进行以下更改：

   * 使用默认[筛选](webjobs-sdk-how-to.md#log-filtering)添加 Application Insights 日志记录提供程序；现在，在本地运行时，所有“信息”和更高级别的日志将转到控制台和 Application Insights。 
   * 在 `using` 块中放置 `LoggerFactory` 对象，确保主机退出时刷新日志输出。 

## <a name="test-application-insights-logging"></a>测试 Application Insights 日志记录

在本部分，我们将再次在本地运行，以验证日志记录数据现在是否转到 Application Insights 和控制台。

1. 像[前面](#trigger-the-function)所做的那样，使用“服务器资源管理器”创建队列消息，不过这次要输入 *Hello App Insights!* 作为消息文本。

1. 运行该项目。

   WebJobs SDK 将处理队列消息，控制台窗口中会显示日志。

1. 关闭控制台窗口。

1. 打开 [Azure 门户](https://portal.azure.com/)并转到 Application Insights 资源。

1. 选择“搜索”。

   ![选择“搜索”](./media/webjobs-sdk-get-started/select-search.png)

1. 如果未看到“Hello App Insights!” 消息，请定期选择“刷新”几分钟。 （日志不会立即显示，因为 Application Insights 客户端需要花费片刻时间来刷新它处理的日志。）

   ![Application Insights 中的日志](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. 关闭控制台窗口。

## <a name="deploy-as-a-webjob"></a>部署 WebJob

在本部分中，我们将项目部署为 WebJob。 将项目部署到[前面创建的](#create-app-service-app-and-application-insights-instance)应用服务应用。 为了在 Azure 中运行项目时测试代码，我们将通过创建一条队列消息来触发函数调用。

1. 在“解决方案资源管理器”中，右键单击项目并选择“发布为 Azure WebJob”。

1. 在“添加 Azure WebJob”对话框中，选择“确定”。

   ![添加 Azure WebJob](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   Visual Studio 会自动安装用于 WebJob 发布的 NuGet 包。

1. 在“发布”向导的“配置文件”步骤中，选择“Microsoft Azure 应用服务”。

   ![“发布”对话框](./media/webjobs-sdk-get-started/publish-dialog.png)

1. 在“应用服务”对话框中，选择“<你的资源组>”>“<你的应用服务应用>”，然后选择“确定”。

   ![“应用服务”对话框](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. 在向导的“连接”步骤中，选择“发布”。

## <a name="trigger-the-function-in-azure"></a>触发 Azure 中的函数

1. 确保不是在本地运行（如果控制台窗口仍旧打开，请将其关闭）。 否则，本地实例可能是处理所创建的任何队列消息的第一个实例。

1. 像[前面](#trigger-the-function)所做的那样，使用“服务器资源管理器”创建队列消息，不过这次要输入 *Hello Azure!*。

1. 刷新 Visual Studio 中的“队列”页，会发现新消息已消失，因为 Azure 应用服务中运行的函数处理了该消息。

   > [!TIP]
   > 若要在 Azure 中进行测试，请使用[开发模式](webjobs-sdk-how-to.md#jobhost-development-settings)来确保立即调用队列触发函数，并避免[队列轮询指数退让](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm)导致的延迟。

### <a name="view-logs-in-application-insights"></a>在 Application Insights 中查看日志

1. 打开 [Azure 门户](https://portal.azure.com/)并转到 Application Insights 资源。

1. 选择“搜索”。

1. 如果未看到“Hello Azure!” 消息，请定期选择“刷新”几分钟。

   查看 WebJob 中运行的函数发出的日志，包括在上一部分中输入的 *Hello Azure!* 文本。

## <a name="add-an-input-binding"></a>添加输入绑定

输入绑定可以简化读取数据的代码。 在本示例中，队列消息是 Blob 名称，我们将使用该 Blob 名称来查找和读取 Azure 存储中的 Blob。

1. 在 *Functions.cs* 中，将 `ProcessQueueMessage` 方法替换为以下代码：

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   在此代码中，`queueTrigger` 是[绑定表达式](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns)，意味着它将在运行时解析为不同的值。  在运行时，它会包含队列消息的内容。

1. 添加 `using`：

   ```cs
   using System.IO;
   ```

1. 在存储帐户中创建 Blob 容器。

   a. 在“服务器资源管理器”中，展开你的存储帐户所在的节点，右键单击“Blob”，并选择“创建 Blob 容器”。

   b. 在“创建 Blob 容器”对话框中，输入 *container* 作为容器名称，然后单击“确定”。

1. 将 *Program.cs* 文件上传到 Blob 容器。 （此处使用的文件用作示例；可以上传任何文本文件，并使用该文件的名称创建队列消息。）

   a. 在“服务器资源管理器”中，双击刚刚创建的容器所在的节点。

   b. 在“容器”窗口中，选择“上传”按钮。

   ![Blob 上传按钮](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. 找到并选择“Program.cs”，然后选择“确定”。

1. 在前面创建的队列中创建队列消息，并使用 *Program.cs* 作为消息的文本。

   ![队列消息 Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. 运行该项目。

   该队列消息会触发函数，而该函数又会读取 Blob 并记录其长度。 控制台输出如下所示：

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>添加输出绑定

输出绑定可以简化写入数据的代码。 本示例在前一个示例的基础上做了修改，它会写入 Blob 的副本，而不是记录其大小。

1. 将 `ProcessQueueMessage`方法替换为以下代码：

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. 创建另一个队列消息，并使用 *Program.cs* 作为消息的文本。

1. 运行该项目。

   该队列消息会触发函数，而该函数又会读取 Blob、记录其长度并创建新 Blob。 控制台输出相同，但在转到 Blob 容器窗口并选择“刷新”时，会看到名为 *copy-Program.cs* 的新 Blob。

## <a name="next-steps"></a>后续步骤

本指南介绍了如何创建、运行和部署 WebJobs SDK 项目。

为了演示 WebJobs SDK 项目的方方面面，本指南中的说明指导我们从头开始创建了一个项目。 但是，在创建下一个项目时，请考虑使用“云”类别中的“Azure WebJob”模板。 此模板会使用已设置好的 NuGet 包和示例代码创建项目。 请注意，可能需要更改示例代码才能使用新的日志记录框架。

有关详细信息，请参阅[如何使用 WebJobs SDK](webjobs-sdk-how-to.md)。
