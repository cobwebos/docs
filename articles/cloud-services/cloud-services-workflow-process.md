---
title: Windows Azure VM 体系结构的工作流 | Microsoft Docs
description: 本文概述部署服务时的工作流过程。
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 5dd57a87658554bf59acf5cee1b6daf67b8692b8
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162149"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Windows Azure 经典 VM 体系结构的工作流 
本文概述当你部署或更新虚拟机等 Azure 资源时发生的工作流过程。 

> [!NOTE]
>Azure 具有用于创建和处理资源的两个不同部署模型：资源管理器部署模型和经典部署模型。 本文介绍使用经典部署模型。

下图显示了 Azure 资源的体系结构。

![Azure 工作流](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>工作流基础知识
   
**答**： RDFE/FFE 是从用户到结构的通信路径。 RDFE（RedDog 前端）是公开的 API，它是管理门户以及 Visual Studio、Azure MMC 等服务管理 API 的前端。  来自用户的所有请求都会通过 RDFE。 FFE（结构前端）是将请求从 RDFE 转换为结构命令的层。 来自 RDFE 的所有请求都会通过 FFE 抵达结构控制器。

**B**. 结构控制器负责维护和监视数据中心内的所有资源。 它与结构 OS 上的、发送来宾 OS 版本、服务包、服务配置和服务状态等信息的结构主机代理通信。

**C**. 主机代理位于主机操作系统上，负责设置来宾操作系统并与来宾代理通信（WindowsAzureGuestAgent），以便将角色更新为目标状态，并使用来宾代理进行检测信号检查。 如果主机代理有 10 分钟未收到检测信号响应，则主机代理会重启来宾 OS。

**C2**. WaAppAgent 负责安装、配置和更新 WindowsAzureGuestAgent.exe。

**D**.  WindowsAzureGuestAgent 负责：

1. 配置来宾 OS，包括防火墙、Acl、LocalStorage 资源、服务包和配置以及证书。
2. 为角色将在其下运行的用户帐户设置 SID。
3. 向结构传达角色状态。
4. 启动并监视 WaHostBootstrapper，以确保角色处于目标状态。

**E**. WaHostBootstrapper 负责：

1. 读取角色配置，并启动所有相应的任务与进程来配置和运行角色。
2. 监视其所有子进程。
3. 针对角色主机进程引发 StatusCheck 事件。

**F**. 如果角色配置为完整的 IIS Web 角色，IISConfigurator 将会运行（它不会针对 SDK 1.2 HWC 角色运行）。 它负责：

1. 启动标准的 IIS 服务
2. 在 Web 配置中配置重写模块
3. 为服务模型中配置的角色设置 AppPool
4. 将 IIS 日志记录设置为指向 DiagnosticStore LocalStorage 文件夹
5. 配置权限和 ACL
6. 网站位于% roleroot%： \sitesroot\0 中，AppPool 指向此位置以运行 IIS。 

**G**. 启动任务由角色模型定义，由 WaHostBootstrapper 启动。 启动任务可配置为以异步方式在后台运行，主机启动程序将启动启动任务，然后继续启动其他启动任务。 启动任务还可以配置为以简单（默认）模式运行，在此模式下，主机启动程序将等待启动任务完成运行，然后返回成功 (0) 退出代码，接着继续启动下一个启动任务。

**H**. 这些任务是 SDK 的一部分，在角色的服务定义 (.csdef) 中定义为插件。 展开到启动任务，**DiagnosticsAgent** 和 **RemoteAccessAgent** 的独特之处在于它们各自定义两个启动任务：一个常规启动任务，一个带有 **/blockStartup** 参数的启动任务。 正常的启动任务定义为后台启动任务，当角色本身正在运行时，该启动任务可在后台运行。 **/blockStartup** 启动任务定义为简单启动任务，WaHostBootstrapper 会先等待它退出，然后才继续。 **/blockStartup** 任务等待常规任务完成初始化，然后退出，并允许主机启动程序继续。 这样，便可以在角色进程启动（通过 /blockStartup 任务来实现）之前配置诊断和 RDP 访问。 同时，允许在主机启动程序完成启动任务（通过普通任务来实现）之后继续运行诊断和 RDP 访问。

**I**. WaWorkerHost 是正常辅助角色的标准主机进程。 此主机进程托管角色的所有 DLL 以及入口点代码，例如 OnStart 和 Run。

**J**. 如果 Web 角色已配置为使用 SDK 1.2 兼容的可托管 Web 核心 (HWC)，则 WaWebHost 是 Web 角色的标准主机进程。 角色可以通过从服务定义 (.csdef) 中删除元素来启用 HWC 模式。 在此模式下，服务的所有代码和 DLL 将从 WaWebHost 进程运行。 不会使用 IIS (w3wp)，并且 IIS 管理器中不会配置任何 AppPool，因为 IIS 托管在 WaWebHost.exe 内部。

**K**. WaIISHost 是使用完整 IIS 的 Web 角色的角色入口点代码的主机进程。 此进程加载找到的第一个 DLL，该 DLL 使用 **RoleEntryPoint** 类并从此类执行代码（OnStart、Run、OnStop）。 在 RoleEntryPoint 类中创建的任何 **RoleEnvironment** 事件（例如 StatusCheck 和 Changed）将在此进程中引发。

**L**. W3WP 是标准的 IIS 工作进程，如果角色配置为使用完整 IIS，将使用该进程。 它运行从 IISConfigurator 配置的 AppPool。 此处创建的任何 RoleEnvironment 事件（例如 StatusCheck 和 Changed）将在此进程中引发。 请注意，如果你订阅了这两个进程中的事件，则 RoleEnvironment 事件将在这两个位置（WaIISHost 和 w3wp.exe）激发。

## <a name="workflow-processes"></a>工作流过程

1. 用户发出请求，如上传 ".cspkg" 和 ".cscfg" 文件、告诉资源停止或进行配置更改等。 可以通过 Azure 门户或者某个使用服务管理 API 的工具（例如 Visual Studio 发布功能）来执行此操作。 此请求将转到 RDFE，以执行所有订阅相关的工作，然后将请求传达给 FFE。 剩余的工作流步骤是部署并启动新包。
2. FFE 查找正确的计算机池（根据客户的输入，例如地缘组或地理位置；以及结构提供的输入，例如计算机可用性），并与该计算机池中的主结构控制器通信。
3. 结构控制器查找具有可用 CPU 核心的主机（或运转新主机）。 服务包和配置将复制到主机，结构控制器与主机 OS 上的主机代理通信，以部署包（配置 DIP、端口、来宾 OS，等等）。
4. 主机代理启动来宾 OS 并与来宾代理 (WindowsAzureGuestAgent) 通信。 主机将检测信号发送到来宾，以确保角色实现其目标状态。
5. WindowsAzureGuestAgent 设置来宾 OS（防火墙、ACL、LocalStorage 等），将新的 XML 配置文件复制到 c:\Config，然后启动 WaHostBootstrapper 进程。
6. 对于完整 IIS Web 角色，WaHostBootstrapper 将启动 IISConfigurator，并告诉它要从 IIS 中删除 Web 角色的所有现有 AppPool。
7. WaHostBootstrapper 从 E:\RoleModel.xml 读取**启动**任务，并开始执行启动任务。 WaHostBootstrapper 等到所有简单启动任务已完成并返回了“成功”消息。
8. 对于完整 IIS Web 角色，WaHostBootstrapper 将告知 IISConfigurator 配置 IIS AppPool 并将站点指向 `E:\Sitesroot\<index>`，其中，`<index>` 是为服务定义的 `<Sites>` 元素数目的从 0 开始的索引。
9. WaHostBootstrapper 将根据角色类型启动主机进程：
    1. **辅助角色**：WaWorkerHost.exe 已启动。 WaHostBootstrapper 执行 OnStart() 方法。 返回后，Wahostbootstrapper.exe 开始执行 Run （）方法，然后将该角色同时标记为 "就绪"，并将其放入负载均衡器循环（如果定义了 InputEndpoints）。 然后，WaHostBootsrapper 进入检查角色状态的循环中。
    1. **SDK 1.2 HWC Web 角色**：WaWebHost 已启动。 WaHostBootstrapper 执行 OnStart() 方法。 返回后，WaHostBootstrapper 开始执行 Run() 方法，同时将角色标记为 Ready，并将其放入负载均衡器轮换阵容。 WaWebHost 发出预热请求 (GET /do.rd_runtime_init)。 所有 Web 请求将发送到 WaWebHost.exe。 然后，WaHostBootsrapper 进入检查角色状态的循环中。
    1. **完整 IIS Web 角色**：aIISHost 已启动。 WaHostBootstrapper 执行 OnStart() 方法。 返回后，它开始执行 Run() 方法，同时将角色标记为 Ready，并将其放入负载均衡器轮换阵容。 然后，WaHostBootsrapper 进入检查角色状态的循环中。
10. 将 Web 请求传入完整 IIS Web 角色会触发 IIS 来启动 W3WP 进程并为请求提供服务，像在本地 IIS 环境中一样。

## <a name="log-file-locations"></a>日志文件位置

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
此日志包含对服务所做的更改，包括启动、停止和新建配置。 如果服务未更改，则你在此日志文件中预期会看到较大的时间差。
- C:\Logs\WaAppAgent.Log.  
此日志包含状态更新和检测信号通知，每隔 2-3 秒更新一次。  此日志包含实例状态的历史视图，告知实例何时不处于就绪状态。
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaWebHost**

`C:\Resources\Directory\<guid>.<role>\WaWebHost.log`
 
**WaIISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**IIS 日志**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Windows 事件日志**

`D:\Windows\System32\Winevt\Logs`
 



