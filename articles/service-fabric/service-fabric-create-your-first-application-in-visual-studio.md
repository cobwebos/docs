<properties
   pageTitle="在 Visual Studio 中创建你的第一个 Service Fabric 应用程序 | Microsoft Azure"
   description="使用 Visual Studio 创建、部署和调试 Service Fabric 应用程序"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="01/11/2016"
   wacn.date=""/>

# 在 Visual Studio 中创建你的第一个 Azure Service Fabric 应用程序

Service Fabric SDK 包含一个用于 Visual Studio 的外接程序，它可提供用于创建、部署和调试 Service Fabric 应用程序的模板和工具。本主题会指导你完成在 Visual Studio 中创建第一个应用程序的过程。

## 先决条件

开始之前，请确保已[设置开发环境](/documentation/articles/service-fabric-get-started)。



## 创建应用程序

Service Fabric 应用程序可以包含一个或多个服务，每个服务都在提供应用程序功能时具有特定角色。使用新建项目向导可以创建应用程序项目以及你的第一个服务项目。以后可以添加更多服务。

1. 以管理员身份启动 Visual Studio。

2. 单击“文件”>“新建项目”>“云”>“Service Fabric 应用程序”。

3. 命名应用程序，然后单击“确定”。

	![Visual Studio 中的新建项目对话框][1]

4. 在下一页上，需要选择要包括在应用程序中的第一种服务类型。对于本教程，我们会选择“有状态”。命名它，然后单击“确定”。

	![Visual Studio 中的新建服务对话框][2]

	>[AZURE.NOTE] 有关选项的详细信息，请参阅[选择框架](/documentation/articles/service-fabric-choose-framework)。

	Visual Studio 会创建应用程序项目和有状态服务项目，并在解决方案资源管理器中显示它们。

	![创建使用有状态服务的应用程序之后的解决方案资源管理器][3]

	应用程序项目不直接包含任何代码。而是引用一组服务项目。此外，它包含三种其他类型的内容：

	- **发布配置文件**：用于为不同环境管理工具首选项。

	- **脚本**：包括用于部署/升级应用程序的 PowerShell 脚本。此脚本由 Visual Studio 在幕后使用，可以在命令行直接调用。

	- **应用程序定义**：包括应用程序清单以及定义应用程序并使你可以专门为给定环境配置它的关联应用程序参数文件。

    有关服务项目的内容概述，请参阅 [Reliable Services 入门](/documentation/articles/service-fabric-reliable-services-quick-start)。

## 部署和调试应用程序

现在已具有应用程序，可以尝试运行它。

1. 按 F5 以部署应用程序以便进行调试。

	>[AZURE.NOTE] 这在首次进行时需要一段时间，因为 Visual Studio 要创建本地群集以用于开发。本地群集只在单台计算机上运行你将在多计算机群集中生成的相同平台代码。你会在 Visual Studio 输出窗口中看到群集创建状态。

	群集准备就绪时，你会从 SDK 随附的本地群集系统托盘管理器应用程序收到通知。

	![本地群集系统托盘通知][4]

2. 应用程序启动之后，Visual Studio 会自动显示诊断事件查看器，在其中可以查看来自服务的跟踪输出。

	![诊断事件查看器][5]

	对于有状态服务模板，消息只显示在 MyStatefulService.cs 的 `RunAsync` 方法中递增的计数器值。

3. 展开事件之一可查看更多详细信息，包括运行代码的节点。在此例中，它是节点 2，不过在你的计算机上可能会有所不同。

	![诊断事件查看器详细信息][6]

	本地群集包含在单台计算机承载的五个节点。它会模拟一个五节点群集，其中节点处于不同计算机上。让我们在本地群集上取下一个节点，以模拟丢失一台计算机的情况并同时练习 Visual Studio 调试器。

    >[AZURE.NOTE] 项目模板发出的应用程序诊断事件会使用包含的 `ServiceEventSource` 类。有关详细信息，请参阅[如何在本地监视和诊断服务](/documentation/articles/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally)。

4. 在服务项目中查找派生自 StatefulService 的类（例如 MyStatefulService），然后在 `RunAsync` 方法的第一行上设置断点。

	![有状态服务 RunAsync 方法中的断点][7]

5. 右键单击本地群集管理器系统托盘应用并选择“管理本地群集”以启动 Service Fabric 资源管理器。

    ![从本地群集管理器启动 Service Fabric 资源管理器][systray-launch-sfx]

    Service Fabric 资源管理器提供群集的可视表示形式 — 包括部署到群集的已部署应用程序的集合和组成群集的物理节点的集合。要了解有关 Service Fabric 资源管理器的详细信息，请参阅[可视化群集](/documentation/articles/service-fabric-visualizing-your-cluster)。

6. 在左窗格中，展开“群集”>“节点”，然后查找运行代码的节点。

7. 单击“操作”>“停用(重新启动)”以模拟计算机重新启动。

	![在 Service Fabric 资源管理器中停止节点][sfx-stop-node]

	随着你在一个节点上进行的计算无缝地故障转移到另一个节点，你应立即在 Visual Studio 中看到命中了断点。

8. 返回到诊断事件查看器并观察消息。请注意，计数器在继续递增，即使事件实际上来自不同的节点。

    ![故障转移之后的诊断事件查看器][diagnostic-events-viewer-detail-post-failover]

### 清理

  结束之前，请务必记住该本地群集非常真实。即使在你停止调试器并关闭 Visual Studio 之后，应用程序也会在后台继续保持运行。根据应用的性质，此后台活动可能会占用计算机上的大量资源。可通过几个选项对此进行管理：

  1. 要移除单个应用程序及其所有数据，请在 Service Fabric 资源管理器中使用“移除应用程序”操作。

  2. 要关闭群集，但保留应用程序数据和跟踪，请在系统托盘应用中单击“停止群集”。

  3. 要完全删除群集，请在系统托盘应用中单击“移除群集”。请注意，此选项会导致下次在 Visual Studio 中按 F5 时部署较慢。仅当在一段时间内不想使用本地群集时，或者当需要回收资源时，才使用此选项。



## 后续步骤

- [参阅如何使用 Web 服务前端向 Internet 服务公开服务](/documentation/articles/service-fabric-add-a-web-frontend)
- [了解如何在 Azure 中创建群集](/documentation/articles/service-fabric-cluster-creation-via-portal)
- [了解有关 Reliable Services 的详细信息](/documentation/articles/service-fabric-reliable-services-quick-start)
- [尝试使用 Reliable Actors 编程模型创建服务](/documentation/articles/service-fabric-reliable-actors-get-started)

<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png

<!---HONumber=Mooncake_0307_2016-->