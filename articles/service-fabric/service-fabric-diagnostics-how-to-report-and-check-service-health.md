<properties
   pageTitle="使用 Azure Service Fabric 报告和检查运行状况 | Microsoft Azure"
   description="了解如何通过服务代码发送运行状况报告，并使用 Azure Service Fabric 提供的运行状况监视工具来检查服务的运行状况。"
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="11/05/2015"
   wacn.date=""/>


# 报告和检查服务运行状况
当服务发生问题时，你必须能够快速检测问题，才能响应并修复所有生成的事件和中断。通过从服务代码向 Service Fabric 运行状况管理器报告问题和失败，你可以使用 Service Fabric 提供的标准运行状况监视工具来检查运行状况。

本文将通过一个示例指导你完成将运行状况报告添加到服务的过程，并说明如何使用 Service Fabric 提供的工具来检查运行状况。本文旨在快速介绍 Service Fabric 中的运行状况监视功能。有关更多详细信息，可以从本文末尾的链接开始，阅读一系列有关运行状况的深入文章。

## 先决条件
必须安装以下软件：
   * Visual Studio 2015
   * Service Fabric SDK

## 部署应用程序并检查其运行状况
若要部署应用程序并检查其运行状况，请执行以下步骤：

1. 以管理员身份启动 Visual Studio。

2. 为有状态服务创建项目。

  ![创建包含有状态服务的 Service Fabric 应用程序](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. 按 **F5** 以调试模式运行应用程序。应用程序将部署到本地群集。

4. 运行应用程序后，在“本地群集管理器”系统托盘应用中单击右键，然后从上下文菜单中选择“管理本地群集”，以启动 Service Fabric 资源管理器。

![从系统托盘启动 Service Fabric 资源管理器](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. 应用程序运行状况应如下图所示。此时，应用程序应该状况良好而没有任何错误。

  ![Service Fabric 资源管理器中运行状况正常的应用程序](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. 也可以使用 PowerShell 来检查运行状况。可以使用 ```Get-ServiceFabricApplicationHealth``` 来检查应用程序的运行状况，使用 ```Get-ServiceFabricServiceHealth``` 来检查服务运行状况。PowerShell 中针对同一应用程序的运行状况报告如下所示。
![PowerShell 中运行状况正常的应用程序](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## 将自定义运行状况事件添加到服务代码
Service Fabric Visual Studio 项目模板包含示例代码。以下步骤说明如何从服务代码报告自定义运行状况事件。此类报告会自动显示在 Service Fabric 提供的标准运行状况监视工具中，例如 Service Fabric 资源管理器、Azure 门户运行状况视图以及 PowerShell。

1. 在 Visual Studio 中重新打开上面创建的应用程序，或者从 Visual Studio 模板使用有状态服务创建新应用程序。
2. 打开 **Stateful1.cs** 文件。到找 `var myDictionary` 的声明，然后紧接在 `var myDictionary` 声明后面添加以下代码。稍后将使用此处创建的 `fabricClient` 对象来报告运行状况。

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    此外，请将此命名空间添加到 **Stateful1.cs** 文件。

    ```csharp
    using System.Fabric;
    ```

4. 接下来，查找 *RunAsync* 方法中的调用 `myDictionary.TryGetValueAsync`。你可以看到，这会返回保存当前计数器值的 `result`，因为此应用程序中的关键逻辑是使计数保持运行。如果这是一个实际的应用程序，并且缺少结果即意味着失败，那么，你可以在运行状况管理器中报告该状况。
5. 若要报告代表失败的缺少结果运行状况事件，请在 `myDictionary.TryGetValueAsync` 调用后面添加以下代码。我们将以 `StatefulServiceReplicaHealthReport` 的形式报告该事件，因为这是有状态服务报告的事件。传入报告事件的 PartitionId 和 ReplicaId 可帮助你在某个运行状况监视工具中查看此报告时识别此报告的源。由于部署的有状态服务可能存在多个分区，而每个分区又可能有多个副本，因此这些参数非常重要。`HealthInformation` 参数存储所要报告的运行状况问题的相关信息。请将此命名空间添加到 **Stateful1.cs** 文件。

    ```csharp
    using System.Fabric.Health;
    ```

    将此代码添加到 `myDictionary.TryGetValueAsync` 调用的后面。

    ```csharp
    if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));

        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

5. 让我们模拟这种失败并看看它如何显示在运行状况监视工具中。若要模拟这种失败，请注释掉前面添加的运行状况报告代码中的第一行。注释掉第一行之后，代码将如下所示。现在，每当执行 RunAsync 时，就会触发此运行状况报告。进行更改之后，请使用 **F5** 运行应用程序。

    ```csharp
    //if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));

        var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

6. 运行应用程序后，打开 Service Fabric 资源管理器检查应用程序的运行状况。这一次，Service Fabric 资源管理器会将应用程序显示为状况不正常。这是因为我们在前面添加的代码报告了错误。
![Service Fabric 资源管理器中运行状况不正常的应用程序](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. 如果在 Service Fabric 资源管理器的树视图中选择主副本，你会看到它也将运行状况显示为出错。其中还显示了已添加到代码中 `HealthInformation` 参数的运行状况报告详细信息。除了 Azure 门户以外，你还可以在 PowerShell 中查看相同的运行状况报告。
![Service Fabric 资源管理器中的副本运行状况](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

此报告将保留在运行状况管理器中，直到被另一份报告替换或此副本被删除。由于我们并未在 HealthInformation 对象中设置此运行状况报告的 TimeToLive，此报告将永不过期。
为了查询和报告运行状况，FabricClient 需要位于拥有管理特权的进程中。

## 后续步骤
[深入了解 Service Fabric 运行状况](/documentation/articles/service-fabric-health-introduction)

<!---HONumber=Mooncake_0314_2016-->