---
title: 使用 Azure Application Insights 监视 Batch | Microsoft Docs
description: 了解如何使用 Azure Application Insights 库检测 Azure Batch .NET 应用程序。
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: danlep
ms.openlocfilehash: 9f989ada01a2ffced509b42df9e46aa001386ab6
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>使用 Application Insights 监视和调试 Azure Batch .NET 应用程序

[Application Insights](../application-insights/app-insights-overview.md) 提供简洁且强大的方式让开发人员监视和调试 Azure 服务中部署的应用程序。 使用 Application Insights 可以监视性能计数器和异常，以及通过自定义指标和跟踪来检测代码。 将 Application Insights 与 Azure Batch 应用程序相集成可以近乎实时地洞察行为以及调查问题。

本文介绍如何在 Azure Batch .NET 解决方案中添加和配置 Application Insights 库，以及检测应用程序代码。 此外，介绍通过 Azure 门户监视应用程序和生成自定义仪表板的方法。 有关 Application Insights 对其他语言的支持，请查看[语言、平台和集成文档](../application-insights/app-insights-platforms.md)。

[GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights) 上提供了一个示例 C# 解决方案，其中随附了本文中演示的代码。 此示例将 Application Insights 检测代码添加到 [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) 示例。 如果你不熟悉该示例，请先尝试生成并运行 TopNWords。 这有助于理解在多个计算节点上并行处理一组输入 Blob 的基本 Batch 工作流。 

## <a name="prerequisites"></a>先决条件
* [Visual Studio IDE](https://www.visualstudio.com/vs)（Visual Studio 2015 或更高版本）

* [Batch 帐户和链接的存储帐户](batch-account-create-portal.md)

* [Application Insights 资源](../application-insights/app-insights-create-new-resource.md)
  
   * 使用 Azure 门户创建一个 Application Insights 资源。 为“应用程序类型”选择“常规”。

   * 从门户中复制[检测密钥](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key)。 本文稍后会用到此密钥。
  
  > [!NOTE]
  > Application Insights 中存储的数据可能会产生[费用](https://azure.microsoft.com/pricing/details/application-insights/)。 这包括本文中所述的诊断和监视数据。
  > 

## <a name="add-application-insights-to-your-project"></a>将 Application Insights 添加到项目

项目需要 **Microsoft.ApplicationInsights.WindowsServer** NuGet 包及其依赖项。 请将其添加或还原到应用程序的项目。 若要安装此包，请使用 `Install-Package` 命令或 NuGet 包管理器。

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
使用 **Microsoft.ApplicationInsights** 命名空间从 .NET 应用程序引用 Application Insights。

## <a name="instrument-your-code"></a>检测代码

若要检测代码，解决方案需要创建 Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient)。 在本示例中，TelemetryClient 将从 [ApplicationInsights.config](../application-insights/app-insights-configuration-with-applicationinsights-config.md) 文件加载其配置。 请务必使用 Application Insights 检测密钥更新以下项目中的 ApplicationInsights.config：Microsoft.Azure.Batch.Samples.TelemetryStartTask 和 TopNWordsSample。

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
此外，请在 TopNWords.cs 文件中添加该检测密钥。

TopNWords.cs 中的示例通过 Application Insights API 使用以下[检测调用](../application-insights/app-insights-api-custom-events-metrics.md)：
* `TrackMetric()` - 跟踪某个计算节点下载所需文本文件平均花费的时间。
* `TrackTrace()` - 将调试调用添加到代码。
* `TrackEvent()` - 跟踪要捕获的相关事件。

本示例有意遗漏了异常处理。 相反，Application Insights 会自动报告未经处理的异常，从而显著改善了调试体验。 

以下代码片段演示如何使用这些方法。

```csharp
public void CountWords(string blobName, int numTopN, string storageAccountName, string storageAccountKey)
{
    // simulate exception for some set of tasks
    Random rand = new Random();
    if (rand.Next(0, 10) % 10 == 0)
    {
        blobName += ".badUrl";
    }

    // log the url we are downloading the file from
    insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Download file from: {1}", this.taskId, blobName), SeverityLevel.Verbose));

    // open the cloud blob that contains the book
    var storageCred = new StorageCredentials(storageAccountName, storageAccountKey);
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), storageCred);
    using (Stream memoryStream = new MemoryStream())
    {
        // calculate blob download time
        DateTime start = DateTime.Now;
        blob.DownloadToStream(memoryStream);
        TimeSpan downloadTime = DateTime.Now.Subtract(start);

        // track how long the blob takes to download on this node
        // this will help debug timing issues or identify poorly performing nodes
        insightsClient.TrackMetric("Blob download in seconds", downloadTime.TotalSeconds, this.CommonProperties);

        memoryStream.Position = 0; //Reset the stream
        var sr = new StreamReader(memoryStream);
        var myStr = sr.ReadToEnd();
        string[] words = myStr.Split(' ');

        // log how many words were found in the text file
        insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Found {1} words", this.taskId, words.Length), SeverityLevel.Verbose));
        var topNWords =
            words.
                Where(word => word.Length > 0).
                GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
                OrderByDescending(x => x.Value).
                Take(numTopN).
                ToList();
        foreach (var pair in topNWords)
        {
            Console.WriteLine("{0} {1}", pair.Key, pair.Value);
        }

        // emit an event to track the completion of the task
        insightsClient.TrackEvent("Done counting words");
    }
}
```

### <a name="azure-batch-telemetry-initializer-helper"></a>Azure Batch 遥测初始化表达式帮助器
报告给定服务器和实例的遥测数据时，Application Insights 使用 Azure VM 角色和 VM 名称作为默认值。 本示例演示如何在 Azure Batch 的上下文中改用池名称和计算节点名称。 使用[遥测初始化表达式](../application-insights/app-insights-api-filtering-sampling.md#add-properties)重写默认值。 

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using System;
using System.Threading;

namespace Microsoft.Azure.Batch.Samples.TelemetryInitializer
{
    public class AzureBatchNodeTelemetryInitializer : ITelemetryInitializer
    {
        // Azure Batch environment variables
        private const string PoolIdEnvironmentVariable = "AZ_BATCH_POOL_ID";
        private const string NodeIdEnvironmentVariable = "AZ_BATCH_NODE_ID";

        private string roleInstanceName;
        private string roleName;

        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                // override the role name with the Azure Batch Pool name
                string name = LazyInitializer.EnsureInitialized(ref this.roleName, this.GetPoolName);
                telemetry.Context.Cloud.RoleName = name;
            }

            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // override the role instance with the Azure Batch Compute Node name
                string name = LazyInitializer.EnsureInitialized(ref this.roleInstanceName, this.GetNodeName);
                telemetry.Context.Cloud.RoleInstance = name;
            }
        }

        private string GetPoolName()
        {
            return Environment.GetEnvironmentVariable(PoolIdEnvironmentVariable) ?? string.Empty;
        }

        private string GetNodeName()
        {
            return Environment.GetEnvironmentVariable(NodeIdEnvironmentVariable) ?? string.Empty;
        }
    }
}
```

为了启用遥测初始化表达式，TopNWordsSample 项目中的 ApplicationInsights.config 文件包含以下值：

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>更新作业和任务，以包含 Application Insights 二进制文件

为使 Application Insights 在计算节点上正常运行，请务必正确放置二进制文件。 将所需的二进制文件添加到任务的资源文件集合，以便在执行任务时下载这些文件。 以下代码片段类似于 Job.cs 中的代码。

首先，创建要上传的 Application Insights 文件的静态列表。

```csharp
private static readonly List<string> AIFilesToUpload = new List<string>()
{
    // Application Insights config and assemblies
    "ApplicationInsights.config",
    "Microsoft.ApplicationInsights.dll",
    "Microsoft.AI.Agent.Intercept.dll",
    "Microsoft.AI.DependencyCollector.dll",
    "Microsoft.AI.PerfCounterCollector.dll",
    "Microsoft.AI.ServerTelemetryChannel.dll",
    "Microsoft.AI.WindowsServer.dll",
    
    // custom telemetry initializer assemblies
    "Microsoft.Azure.Batch.Samples.TelemetryInitializer.dll",
 };
...
```

接下来，创建任务使用的临时文件。
```csharp
...
// create file staging objects that represent the executable and its dependent assembly to run as the task.
// These files are copied to every node before the corresponding task is scheduled to run on that node.
FileToStage topNWordExe = new FileToStage(TopNWordsExeName, stagingStorageAccount);
FileToStage storageDll = new FileToStage(StorageClientDllName, stagingStorageAccount);

// Upload Application Insights assemblies
List<FileToStage> aiStagedFiles = new List<FileToStage>();
foreach (string aiFile in AIFilesToUpload)
{
    aiStagedFiles.Add(new FileToStage(aiFile, stagingStorageAccount));
}
...
```

`FileToStage` 方法是代码示例中的帮助器函数，用于将文件从本地磁盘轻松上传到 Azure 存储 Blob。 稍后每个文件将下载到计算节点，并由任务引用。

最后，将任务添加到作业，并包含所需的 Application Insights 二进制文件。
```csharp
...
// initialize a collection to hold the tasks that will be submitted in their entirety
List<CloudTask> tasksToRun = new List<CloudTask>(topNWordsConfiguration.NumberOfTasks);
for (int i = 1; i <= topNWordsConfiguration.NumberOfTasks; i++)
{
    CloudTask task = new CloudTask("task_no_" + i, String.Format("{0} --Task {1} {2} {3} {4}",
        TopNWordsExeName,
        string.Format("https://{0}.blob.core.windows.net/{1}",
            accountSettings.StorageAccountName,
            documents[i]),
        topNWordsConfiguration.TopWordCount,
        accountSettings.StorageAccountName,
        accountSettings.StorageAccountKey));

    //This is the list of files to stage to a container -- for each job, one container is created and 
    //files all resolve to Azure Blobs by their name (so two tasks with the same named file will create just 1 blob in
    //the container).
    task.FilesToStage = new List<IFileStagingProvider>
                        {
                            // required application binaries
                            topNWordExe,
                            storageDll,
                        };
    foreach (FileToStage stagedFile in aiStagedFiles)
   {
        task.FilesToStage.Add(stagedFile);
   }    
    task.RunElevated = false;
    tasksToRun.Add(task);
}
```

## <a name="view-data-in-the-azure-portal"></a>在 Azure 门户中查看数据

将作业和任务配置为使用 Application Insights 后，请在池中运行示例作业。 导航到 Azure 门户，并打开预配的 Application Insights 资源。 预配池后，应会开始看到数据流动并被记录。 本文的余下内容只会讨论几项 Application Insights 功能，读者可以任意探索整个功能集。

### <a name="view-live-stream-data"></a>查看实时流数据

若要查看 Application Insights 资源中的跟踪日志，请单击“实时流”。 以下屏幕截图显示如何查看来自池中计算节点的实时数据，例如每个计算节点的 CPU 使用率。

![实时流计算节点数据](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>查看跟踪日志

若要查看 Application Insights 资源中的跟踪日志，请单击“搜索”。 此视图显示 Application Insights 捕获的诊断数据列表，包括跟踪、事件和异常。 

以下屏幕截图显示如何记录某个任务的单个跟踪，并随后对其进行查询以实现调试目的。

![跟踪日志图像](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>查看未经处理的异常

以下屏幕截图显示 Application Insights 如何记录应用程序中引发的异常。 在本例中，在应用程序引发异常后的几秒钟内，即可深入到特定的异常并诊断问题。

![未经处理的异常](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>测量 Blob 下载时间

自定义指标也是门户中的一个有用工具。 例如，可以显示每个计算节点下载其处理的所需文本文件平均花费的时间。

创建示例图表：
1. 在 Application Insights 资源中，单击“指标资源管理器” > “添加图表”。
2. 在添加的图表上单击“编辑”。
2. 按如下所示更新图表详细信息：
   * 将“图表类型”设置为“网格”。
   * 将“聚合”设置为“平均”。
   * 将“分组依据”设置为“NodeId”。
   * 在“指标”中，选择“自定义” > “Blob 下载时间(秒)”。
   * 根据偏好调整“调色板”的显示。 

![每个节点的 Blob 下载时间](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>连续监视计算节点

你可能已注意到，仅当任务运行时，才会记录所有指标，包括性能计数器。 此行为很有用，因为它会限制 Application Insights 记录的数据量。 但是，在某些情况下，我们希望一直监视计算节点。 例如，计算节点可能在运行未由 Batch 服务计划的后台工作。 在这种情况下，可设置为在计算节点的整个生命周期内都要运行某个监视进程。 

实现此行为的方法之一是生成一个加载 Application Insights 库并在后台运行的进程。 在本示例中，启动任务在计算机上加载二进制文件，并使某个进程无限期运行。 将此进程的 Application Insights 配置文件配置为发出所需的附加数据，例如性能计数器。

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = "StartTask";
private const string BatchStartTaskTelemetryRunnerName = "Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe";
private const string BatchStartTaskTelemetryRunnerAIConfig = "ApplicationInsights.config";
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: "small",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));
...

// Create a start task which will run a dummy exe in background that simply emits performance
// counter data as defined in the relevant ApplicationInsights.config.
// Note that the waitForSuccess on the start task was not set so the Compute Node will be
// available immediately after this command is run.
pool.StartTask = new StartTask()
{
    CommandLine = string.Format("cmd /c {0}", BatchStartTaskTelemetryRunnerName),
    ResourceFiles = resourceFiles
};
...
```

> [!TIP]
> 若要提高解决方案的可管理性，可以在[应用程序包](./batch-application-packages.md)中捆绑程序集。 然后，若要自动将应用程序包部署到池中，请添加对池配置的应用程序包引用。
>

## <a name="throttle-and-sample-data"></a>限制和示例数据 

由于生产环境中运行的 Azure Batch 应用程序的大规模性，你可能想要限制 Application Insights 收集的数据量，以控制成本。 请参阅[在 Application Insights 中采样](../application-insights/app-insights-sampling.md)，了解一些可以实现此目的的机制。


## <a name="next-steps"></a>后续步骤
* 详细了解 [Application Insights](../application-insights/app-insights-overview.md)。

* 有关 Application Insights 对其他语言的支持，请查看[语言、平台和集成文档](../application-insights/app-insights-platforms.md)。


