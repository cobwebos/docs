---
title: 工作流的 Windows Azure VM 体系结构 |Microsoft Docs
description: 部署服务时，这篇文章提供了工作流过程的概述。
services: cloud-services
documentationcenter: ''
author: genlin
manager: Willchen
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 7c8459a6694663a49203b6ec21a760d3e6bd60c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480735"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Windows Azure 经典 VM 体系结构的工作流 
本文概述了部署或更新虚拟机等 Azure 资源时出现的工作流进程。 

> [!NOTE]
>Azure 具有用于创建和处理资源的两个不同部署模型：资源管理器部署模型和经典部署模型。 本文介绍使用经典部署模型。

下图显示 Azure 资源的体系结构。

![Azure 工作流](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>工作流基础知识
   
**答**： RDFE / FFE 是从用户到构造的通信路径。 RDFE （RedDog 前端） 是到管理门户的前端公开的 API 和服务管理 API，如 Visual Studio、 Azure MMC 中，依次类推。  从用户的所有请求都都通过 RDFE。 FFE （Fabric 前端） 是将请求转换从 RDFE 成 fabric 命令的层。 从 RDFE 的所有请求都都通过 FFE 以访问控制器的构造。

**B**。 结构控制器负责维护和监视数据中心中的所有资源。 它与 OS 的来宾 OS 版本、 服务包、 服务配置和服务状态等信息发送在结构上的 fabric 主机代理通信。

**C**。 主机代理上。 主机 OSsystem，负责来宾操作系统设置，并与来宾代理 (WindowsAzureGuestAgent) 以更新到预期的目标状态的角色和执行检测信号通信检查与来宾代理。 如果主机代理 10 分钟内未收到检测信号响应，主机代理重新启动来宾操作系统。

**C2**。 WaAppAgent 负责安装、 配置和更新 WindowsAzureGuestAgent.exe。

**D**。  WindowsAzureGuestAgent 负责以下：

1. 配置来宾 OS 包括防火墙、 Acl、 LocalStorage 资源、 服务包和配置和证书。设置将用于运行角色的用户帐户的 SID。
2. 在构造与通信的角色状态。
3. 启动 WaHostBootstrapper 和监视，以确保该角色处于目标状态。

**E**。 WaHostBootstrapper 负责：

1. 读取角色配置和启动所有相应的任务和进程，若要配置和运行角色。
2. 监视其所有子进程。
3. 引发 StatusCheck 事件上角色主机进程。

**F**。 如果将角色配置为完整 IIS web 角色 （它不会为 SDK 1.2 HWC 角色运行），在运行 IISConfigurator。 它负责：

1. 正在启动标准的 IIS 服务
2. 在 web 配置中配置重写模块
3. 设置配置的角色服务模型中的应用程序池
4. 设置 IIS 日志记录以指向 DiagnosticStore LocalStorage 文件夹
5. 配置权限和 Acl
6. 该网站保存在 %roleroot%:\sitesroot\0 以及指向此位置以运行 IIS 应用程序池。 

**G**。 通过角色模型定义并由 WaHostBootstrapper 启动启动任务。 启动任务可以配置为在后台以异步方式运行和主机引导程序将从开始启动任务，然后继续学习其他启动任务。 启动任务还可以配置为在该主机引导程序将等待启动任务以完成运行并继续操作之前的 (0) 成功退出代码返回到下一步的启动任务的简单 （默认值） 模式下运行。

**H**。 这些任务是 SDK 的一部分，定义为角色的服务定义 (.csdef) 中的插件。 为启动任务中，展开**DiagnosticsAgent**并**RemoteAccessAgent**是唯一的它们每个定义两个启动任务、 一个常规模式和一个具有 **/blockStartup**参数。 正常的启动任务被指后台启动任务，以便它可以在后台运行的角色本身正在运行时。 **/BlockStartup**启动任务定义为 Simple 启动任务以便 WaHostBootstrapper 等待它退出然后再继续。 **/BlockStartup**任务等待完成初始化时，常规任务，然后退出，并允许主机引导程序以继续。 这样，以便可以在角色进程启动 （这是通过 /blockStartup 任务） 之前配置诊断和 RDP 访问。 这也允许诊断和 RDP 访问主机引导程序已完成 （这是通过普通的任务） 的启动任务之后继续运行。

**我**。 WaWorkerHost 是正常的辅助角色的标准主机进程。 此主机进程承载角色的所有 Dll 和入口点代码，如 OnStart 和运行。

**J**。 WaWebHost 是 web 角色的标准主机进程，如果它们已配置为使用 SDK 1.2 兼容可承载 Web 核心 (HWC)。 角色可以通过从服务定义 (.csdef) 中删除元素启用 HWC 模式。 在此模式下，服务的所有代码和 Dll 运行从 WaWebHost 进程。 不使用 IIS (w3wp)，并且没有任何应用程序池配置在 IIS 管理器，因为 IIS 承载于 WaWebHost.exe 内。

**K**。 WaIISHost 是使用完整 IIS 的 web 角色的角色入口点代码的主机进程。 此过程将使用找到的第一个 DLL 加载**RoleEntryPoint**类，并从此类 （OnStart、 运行、 OnStop） 执行的代码。 任何**RoleEnvironment** RoleEntryPoint 类中创建的事件 （如 StatusCheck 和已更改） 在此过程中引发。

**L**。 W3WP 是如果将角色配置为使用完整 IIS，则使用标准的 IIS 工作进程。 这将从 IISConfigurator 运行应用程序池配置。 在此过程中引发此处创建任何 RoleEnvironment 事件 （如 StatusCheck 和已更改）。 请注意，如果你订阅了这两个进程中的事件，触发 RoleEnvironment 事件将使用的这两个位置 （WaIISHost 和 w3wp.exe）。

## <a name="workflow-processes"></a>工作流进程

1. 用户发出请求，或上传.cspkg 和.cscfg 文件，告知要停止的资源进行配置更改，例如，依此类推。 这可以通过 Azure 门户或使用服务管理 API，如 Visual Studio 发布功能的工具来完成。 所有与订阅相关工作，然后进行通信对 FFE 的请求，此请求将转到 RDFE 来执行操作。 这些工作流步骤的其余部分是部署新的包并启动它。
2. FFE 找到正确的计算机池 （基于客户输入此类中，为地缘组或地理位置加上通过构造，例如计算机可用性的输入），并与该计算机池中 master 结构控制器进行通信。
3. 结构控制器将查找具有可用的 CPU 内核的主机 （或数量最多一个新的主机）。 服务包和配置复制到主机，并与主机 OS 部署的包上的主机代理进行通信的结构控制器 （配置 Dip、 端口、 来宾操作系统，等等）。
4. 主机代理启动来宾操作系统，并与来宾代理 (WindowsAzureGuestAgent) 进行通信。 主机将检测信号发送到来宾操作系统以确保该角色正在向其目标状态。
5. WindowsAzureGuestAgent 设置来宾操作系统 （如防火墙、 Acl、 LocalStorage，等），将新的 XML 配置文件复制到 c:\Config，，然后开始 WaHostBootstrapper 过程。
6. 对于完整 IIS web 角色，WaHostBootstrapper 启动 IISConfigurator 并告诉它从 IIS 删除 web 角色的任何现有应用程序池。
7. 读取 WaHostBootstrapper**启动**从 E:\RoleModel.xml 任务，并开始执行启动任务。 WaHostBootstrapper 等待，直到所有简单的启动任务已完成，并且返回"成功"消息。
8. 对于完整 IIS web 角色，WaHostBootstrapper 告知 IISConfigurator 若要配置 IIS 应用程序池和点到站点`E:\Sitesroot\<index>`，其中`<index>`是 0 开始的索引数成<Sites>为服务定义的元素。
9. WaHostBootstrapper 将启动主机进程，具体取决于角色类型：
    1. **辅助角色**:WaWorkerHost.exe 已启动。 WaHostBootstrapper 执行 onstart （） 方法。它返回后，WaHostBootstrapper 开始执行 run （） 方法，同时将标记为 '就绪' 角色并将其放入负载均衡器轮转 （如果定义了 InputEndpoints）。 WaHostBootsrapper 然后转入检查角色状态的循环。
    1. **SDK 1.2 HWC Web 角色**:WaWebHost 已启动。 WaHostBootstrapper 执行 onstart （） 方法。 它返回后，WaHostBootstrapper 开始执行 run （） 方法，并为 '就绪' 角色，同时将标记并将其放到负载均衡器轮转。 WaWebHost 发出预热请求 (GET /do.rd_runtime_init)。 所有 web 请求都发送到 WaWebHost.exe。 WaHostBootsrapper 然后转入检查角色状态的循环。
    1. **完整的 IIS Web 角色**: aIISHost 已启动。 WaHostBootstrapper 执行 onstart （） 方法。 它返回后，它并开始执行 run （） 方法，然后同时将标记为 '就绪' 角色并将其放到负载均衡器轮转。 WaHostBootsrapper 然后转入检查角色状态的循环。
10. 传入 web 请求到完整 IIS web 角色触发器 IIS 启动 W3WP 进程并处理请求，像在本地 IIS 环境中相同。

## <a name="log-file-locations"></a>日志文件位置

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
此日志包含对服务包括启动、 停止，并且新的配置更改。 如果该服务不会更改，您有望看到此日志文件中大的时间间隔。
- C:\Logs\WaAppAgent.Log.  
此日志包含状态更新和检测信号通知，并更新每隔 2-3 秒。  此日志包含的实例的状态的历史视图，并告诉您实例时不处于就绪状态。
 
**WaHostBootstrapper**

C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log
 
**WaWebHost**

C:\Resources\Directory\<guid>.<role>\WaWebHost.log
 
**WaIISHost**

C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log
 
**IISConfigurator**

C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log
 
**IIS 日志**

C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1
 
**Windows 事件日志**

D:\Windows\System32\Winevt\Logs
 



