<properties
   pageTitle="Microsoft Azure Service Fabric 如何本地监视和诊断服务"
   description="本文介绍如何监视和诊断本地开发计算机上的使用 Microsoft Azure Service Fabric 编写的服务。"
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="07/22/2015"
   wacn.date=""/>


# 在本地计算机开发安装过程中监视和诊断服务
监视、检测、诊断和故障排除允许服务继续，同时对用户体验造成最小中断。当它在实际部署的生产环境中关键时，效力将取决于在服务开发过程中采用类似的模型，以确保在你移到实际安装时，它将正常工作。Service Fabric 使服务开发人员能够轻松实现可以跨单个计算机本地开发和实际生产群集安装无缝工作的诊断。

## 跟踪和日志记录
[Windows 事件跟踪](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) 是推荐的用于跟踪 Service Fabric 中的消息的技术。这是因为：

* ETW 速度很快。它是作为一种对你的代码执行时间影响最小的跟踪技术而构建的。
* ETW 跟踪可以跨本地开发环境和实际群集安装无缝工作。这意味着当你准备好将你的代码部署到实际群集时，无需重新编写跟踪代码。
* Service Fabric 系统代码也使用 ETW 进行内部跟踪。这允许你查看与 Service Fabric 系统跟踪交错在一起的应用程序跟踪，从而更容易理解基础系统中应用程序代码与事件之间的先后顺序和相互关系。
* Service Fabric Visual Studio 工具中有内置支持，可以查看 ETW 事件。


## 在 Visual Studio 中查看 Service Fabric 系统事件

Service Fabric 发出 ETW 事件以帮助应用程序开发人员了解平台中发生的情况。若要查看这些事件，请执行以下步骤：

1. 必须安装以下必备组件。
   * Visual Studio 2015
   * Service Fabric SDK

2. 以管理员身份启动 Visual Studio。

3. 为有状态或无状态 Actor 或 Service 创建（或打开现有）项目。

  ![创建 Service Fabric 应用程序](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/CreateServiceFabricProject.png)

  ![创建 Service Fabric 服务](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/CreateServiceFabricProject-2.png)

4. 按 F5 调试应用程序。Service Fabric 事件应显示在“诊断事件”窗口中。每个事件都有标准元数据信息，它将告诉你事件来自的节点、应用程序和服务。你还可以使用窗口顶部的“筛选事件”框来筛选事件列表，例如，你可以按“节点名称”或“服务名称”筛选。

  ![Visual Studio 诊断事件查看器](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

5. 如果“诊断事件”窗口不自动显示，请转到 Visual Studio 中的“服务器资源管理器”选项卡，右键单击 Service Fabric 群集，然后在上下文菜单中选择“查看诊断事件”。

  ![打开 Visual Studio 诊断事件查看器](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/ServerExViewDiagEvents.png)

## 将你自己的自定义跟踪添加到应用程序代码
Service Fabric Visual Studio 项目模板包含示例代码。示例代码演示如何添加自定义应用程序代码 ETW 跟踪，它与来自 Service Fabric 的系统跟踪一起显示在 Visual Studio ETW 查看器中。此方法的优点是元数据将自动添加到跟踪，并已配置了 Visual Studio 诊断查看器来显示它们。

对于从**Service 模板**（无状态或有状态）创建的项目：

1. 打开 **Service.cs** 文件。对 *RunAsync* 方法中的 `ServiceEventSource.Current.Message` 的调用显示了应用程序代码中的自定义 ETW 跟踪的一个示例。
2. 在 **ServiceEventSource.cs** 文件中，`ServiceEventSource.Message` 方法的重载显示如何编写自定义 ETW 跟踪的选项。

对于从**Actor 模板**（无状态或有状态）创建的项目：

1. 打开 **"ProjectName".cs** 文件，其中，*ProjectName* 是为你的 Visual Studio 项目选择的名称。  
2. 在 *DoWorkAsync* 方法中查找代码 `ActorEventSource.Current.ActorMessage(this, "Doing Work");`。这是应用程序代码中的自定义 ETW 跟踪的一个示例。  
3. 在 **ActorEventSource.cs** 文件中，`ActorEventSource.ActorMessage` 方法的重载显示如何编写自定义 ETW 跟踪的选项。

将自定义 ETW 跟踪添加到你的服务代码之后，可以再次生成、部署和运行应用程序以在诊断查看器中查看你的事件。如果使用 F5 调试应用程序，则将自动打开诊断查看器。

##即将支持
你添加到上述应用程序用于进行本地诊断的相同跟踪代码将与工具一起工作，在 Azure 群集上运行相同的代码时，你可以使用这些工具查看这些事件。详情敬请期待。

## 后续步骤

* [Service Fabric 运行状况简介](/documentation/articles/service-fabric-health-introduction)
* [Application Insights 安装](/documentation/articles/service-fabric-diagnostics-application-insights-setup)
* [Azure Service Fabric Actors 诊断和性能监视](/documentation/articles/service-fabric-reliable-actors-diagnostics)
* [有状态的 Reliable Service 诊断](/documentation/articles/service-fabric-reliable-services-diagnostics)

<!---HONumber=74-->