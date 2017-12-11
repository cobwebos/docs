---
title: "使用 C# 创建 Azure Service Fabric Reliable Service"
description: "使用 Visual Studio 创建、部署和调试基于 Azure Service Fabric 的 Reliable Service 应用程序。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: c3655b7b-de78-4eac-99eb-012f8e042109
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/04/2017
ms.author: ryanwi
ms.openlocfilehash: 70d365910569a2acb1c230c803fdfca5cb6b35af
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2017
---
# <a name="create-your-first-c-service-fabric-stateful-reliable-services-application"></a>创建你的第一个 C# Service Fabric 有状态 Reliable Services 应用程序

了解如何在数分钟内在 Windows 上部署你的第一个适用于 .NET 的 Service Fabric 应用程序。 完成后，你会有一个本地群集，与 Reliable Service 应用程序一起运行。

## <a name="prerequisites"></a>先决条件

开始之前，请确保已 [设置开发环境](service-fabric-get-started.md)。 其中包括安装 Service Fabric SDK 和 Visual Studio 2017 或 2015。

## <a name="create-the-application"></a>创建应用程序

以管理员身份启动 Visual Studio。

使用 `CTRL`+`SHIFT`+`N` 创建一个项目

在“新建项目”对话框中，选择“云”>“Service Fabric 应用程序”。

将应用程序命名为“MyApplication”，然后按“确定”。

   
![Visual Studio 中的新建项目对话框][1]

可以在下一对话框中创建任何类型的 Service Fabric 应用程序。 对于本快速入门教程，请选择“有状态服务”。

将服务命名为“MyStatefulService”，然后按“确定”。

![Visual Studio 中的新建服务对话框][2]


Visual Studio 会创建应用程序项目和有状态服务项目，并在解决方案资源管理器中显示它们。

![创建使用有状态服务的应用程序之后的解决方案资源管理器][3]

应用程序项目 (MyApplication) 不直接包含任何代码。 而是引用一组服务项目。 此外，它包含三种其他类型的内容：

* 发布配置文件  
部署到不同环境所需的配置文件。

* 脚本  
用于部署/升级应用程序的 PowerShell 脚本。

* 应用程序定义  
在 ApplicationPackageRoot 下添加说明应用程序组合情况的 ApplicationManifest.xml 文件。 关联的应用程序参数文件位于 ApplicationParameters 下，这些文件可以用来指定特定于环境的参数。 Visual Studio 会在将内容部署到特定环境时，选择在关联的发布配置文件中指定的应用程序参数文件。
    
有关服务项目的内容概述，请参阅 [Reliable Services 入门](service-fabric-reliable-services-quick-start.md)。

## <a name="deploy-and-debug-the-application"></a>部署和调试应用程序

有了应用程序后，请运行它。

在 Visual Studio 中按 `F5`，部署用于调试的应用程序。

>[!NOTE]
>首次在本地运行和部署应用程序时，Visual Studio 会创建用于调试的本地群集。 这可能需要一些时间。 群集创建状态显示在 Visual Studio 输出窗口中。

群集准备就绪时，将从 SDK 随附的本地群集系统托盘管理器应用程序收到通知。
   
![本地群集系统托盘通知][4]

应用程序启动后，Visual Studio 会自动显示诊断事件查看器，可以在其中查看来自服务的跟踪输出。
   
![诊断事件查看器][5]

我们使用的有状态服务模板直接显示在 MyStatefulService.cs 的 `RunAsync` 方法中递增的计数器值。

展开事件之一可查看更多详细信息，包括运行代码的节点。 在此例中，它是 \_Node\_0，不过在你的计算机上可能会有所不同。
   
![诊断事件查看器详细信息][6]

本地群集包含在单台计算机上托管的五个节点。 在生产环境中，每个节点都托管在不同的物理或虚拟机上。 在本地群集上取下一个节点，以模拟丢失一台计算机的情况，同时练习 Visual Studio 调试器。

在“解决方案资源管理器”窗口中，打开“MyStatefulService.cs”。 

找到 `RunAsync` 方法，在该方法的第一行中设置一个断点。

![有状态服务 RunAsync 方法中的断点][7]

右键单击“本地群集管理器”系统托盘应用程序并选择“管理本地群集”，以便启动 Service Fabric Explorer 工具。

![从本地群集管理器启动 Service Fabric 资源管理器][systray-launch-sfx]

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 提供以视觉方式呈现群集的功能。 它包括部署到其中的应用程序集，以及充当组成成分的物理节点集。

在左窗格中，展开“群集”>“节点”，并查找运行代码的节点。

单击“操作”>“停用（重启）”来模拟计算机重启。

![在 Service Fabric 资源管理器中停止节点][sfx-stop-node]

随着你在一个节点上进行的计算无缝地故障转移到另一个节点，应立即在 Visual Studio 中看到命中了断点。


接下来，返回到诊断事件查看器并观察消息。 计数器在继续递增，即使事件实际上来自不同的节点。

![故障转移之后的诊断事件查看器][diagnostic-events-viewer-detail-post-failover]

## <a name="cleaning-up-the-local-cluster-optional"></a>清理本地群集（可选）

请记住，此本地群集是真实的。 停止调试器会删除应用程序实例，并注销应用程序类型。 不过，群集会继续在后台运行。 做好停止本地群集的准备以后，有多个选项可以选择。

### <a name="keep-application-and-trace-data"></a>保留应用程序和跟踪数据

右键单击“本地群集管理器”系统托盘应用程序，然后选择“停止本地群集”，以便关闭群集。

### <a name="delete-the-cluster-and-all-data"></a>删除群集和所有数据

右键单击“本地群集管理器”系统托盘应用程序，然后选择“删除本地群集”，以便删除群集。 

如果选择此选项，Visual Studio 会在你下次运行应用程序时重新部署群集。 如果在一段时间内不想使用本地群集，或者需要回收资源，请选择此选项。

## <a name="next-steps"></a>后续步骤
阅读有关 [Reliable Services](service-fabric-reliable-services-introduction.md) 的更多内容。
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
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
