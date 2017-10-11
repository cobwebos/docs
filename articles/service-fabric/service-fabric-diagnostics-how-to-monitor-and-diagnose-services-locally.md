---
title: "调试在 Windows Azure 微服务 |Microsoft 文档"
description: "了解如何监视和诊断本地开发计算机上使用 Microsoft Azure Service Fabric 编写的服务。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2017
ms.author: dekapur
ms.openlocfilehash: 08998340afb2f242b9a268331607b0d1ddb9b0c6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>监视和诊断在本地计算机开发安装程序中的服务
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

监视、 检测、 诊断和故障排除允许服务继续向用户体验的很少发生中断。 关键的实际部署的生产环境中监视和诊断时，效率将依赖于服务以确保它们工作移到实际的安装程序时开发过程中采用类似的模型。 Service Fabric 轻松服务开发人员来实现可以跨单个计算机本地开发的设置和实际生产群集安装无缝工作的诊断。

## <a name="event-tracing-for-windows"></a>Windows 事件跟踪
[Windows 事件跟踪](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx)(ETW) 是 Service Fabric 中的跟踪消息的推荐的技术。 使用 ETW 的一些好处包括：

* **ETW 速度很快。** 它是对代码执行时间的影响最小的跟踪技术构建的。
* **ETW 跟踪可以跨本地开发环境和实际群集安装无缝工作。** 这意味着无需重新编写跟踪代码，当你准备好将你的代码部署到实际群集。
* **Service Fabric 系统代码也使用 ETW 进行内部跟踪。** 这样，您可以查看与 Service Fabric 系统跟踪交错的应用程序跟踪。 它还可帮助你更轻松地在基础系统中，了解序列和你的应用程序代码和事件之间的相互关系。
* **这是 Service Fabric Visual Studio 工具查看 ETW 事件中的内置支持。** 使用 Service Fabric 正确配置 Visual Studio 后，ETW 事件将出现在 Visual Studio 的诊断事件视图。 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>在 Visual Studio 中查看 Service Fabric 系统事件
Service Fabric 发出 ETW 事件以帮助应用程序开发人员了解平台中发生了什么情况。 如果你尚未这样做，请继续并按照中的步骤[在 Visual Studio 中创建第一个应用程序](service-fabric-create-your-first-application-in-visual-studio.md)。 此信息将帮助你获得应用程序启动并正在运行，以及显示的跟踪消息的诊断事件查看器。

1. 如果的诊断事件窗口不自动显示，请转到**视图**Visual Studio 中的选项卡上，选择**其他窗口**然后**诊断事件查看器**。
2. 每个事件都有标准元数据信息，告诉你节点、 应用程序和事件来自的服务。 此外可以通过使用筛选的事件列表**筛选事件**事件窗口顶部的框。 例如，你可以筛选**节点名称**或**服务名称。** 和在您要查看事件详细信息，你可以暂停使用**暂停**按钮顶部的事件窗口和更高版本继续而不丢失任何事件。
   
   ![Visual Studio 诊断事件查看器](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>将你自己的自定义跟踪添加到应用程序代码
Service Fabric Visual Studio 项目模板包含示例代码。 代码演示如何添加在 Visual Studio ETW 查看器中与系统跟踪一起显示来自 Service Fabric 的自定义应用程序代码 ETW 跟踪。 此方法的优点是，元数据将自动添加到跟踪，并且 Visual Studio 诊断事件查看器已被配置为显示它们。

从创建的项目**服务模板**（无状态或有状态） 只需搜索`RunAsync`实现：

1. 调用`ServiceEventSource.Current.ServiceMessage`中`RunAsync`方法显示应用程序代码中的自定义 ETW 跟踪的示例。
2. 在**ServiceEventSource.cs**文件，你将找到的重载`ServiceEventSource.ServiceMessage`应该用于高频率由于性能原因的事件的方法。

从创建的项目**actor 模板**（无状态或有状态）：

1. 打开**"ProjectName".cs**文件，其中*ProjectName*是为你的 Visual Studio 项目选择的名称。  
2. 查找代码`ActorEventSource.Current.ActorMessage(this, "Doing Work");`中*DoWorkAsync*方法。  这是一个示例应用程序代码中编写的自定义 ETW 跟踪。  
3. 文件中**ActorEventSource.cs**，你将找到的重载`ActorEventSource.ActorMessage`应该用于高频率由于性能原因的事件的方法。

将自定义 ETW 跟踪添加到你的服务代码之后, 可以生成、 部署和运行应用程序以查看你在诊断事件查看器中的事件。 如果调试的应用程序**F5**，诊断事件查看器将自动打开。

## <a name="next-steps"></a>后续步骤
添加到上述应用程序用于进行本地诊断的相同跟踪代码会使用可用于查看这些事件时在 Azure 群集上运行你的应用程序的工具。 签出这些文章讨论工具的不同选项，描述如何你可以设置它们的文章。

* [如何使用 Azure 诊断的日志收集](service-fabric-diagnostics-how-to-setup-wad.md)
* [事件聚合和使用 EventFlow 集合](service-fabric-diagnostics-event-aggregation-eventflow.md)

