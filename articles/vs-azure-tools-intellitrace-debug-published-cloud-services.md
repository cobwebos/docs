---
title: "使用 Visual Studio 和 IntelliTrace 调试已发布的 Azure 云服务 | Microsoft Docs"
description: "了解如何使用 Visual Studio 和 IntelliTrace 调试云服务"
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: ghogen
editor: 
ms.assetid: 5e6662fc-b917-43ea-bf2b-4f2fc3d213dc
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/21/2017
ms.author: mikejo
ms.openlocfilehash: d33a8338b37f7479196449238388c7dbf391bbb6
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2017
---
# <a name="debugging-a-published-azure-cloud-service-with-visual-studio-and-intellitrace"></a>使用 Visual Studio 和 IntelliTrace 调试已发布的 Azure 云服务
通过 IntelliTrace，可以在某个角色实例在 Azure 中运行时记录该角色实例的大量调试信息。 如果需要查找问题的原因，可以从 Visual Studio 使用 IntelliTrace 日志来单步执行代码，就像它在 Azure 中运行一样。 实际上，当 Azure 应用程序以云服务的形式在 Azure 中运行时，IntelliTrace 将记录关键代码执行和环境数据，并允许从 Visual Studio 中回放已记录的数据。 

如果已安装 Visual Studio Enterprise，且 Azure 应用程序以 .NET Framework 4 或更高版本为目标，则可使用 IntelliTrace。 IntelliTrace 收集 Azure 角色的信息。 这些角色的虚拟机始终运行 64 位操作系统。

作为替代方法，可以使用[远程调试](http://go.microsoft.com/fwlink/p/?LinkId=623041)，直接连接到在 Azure 中运行的云服务。

> [!IMPORTANT]
> IntelliTrace 仅适用于调试方案，而不应用于生产部署。
> 

## <a name="configure-an-azure-application-for-intellitrace"></a>为 IntelliTrace 配置 Azure 应用程序
若要为 Azure 应用程序启用 IntelliTrace，必须从 Visual Studio Azure 项目创建并发布应用程序。 在将应用程序发布到 Azure 之前，必须为 Azure 应用程序配置 IntelliTrace。 如果在未配置 IntelliTrace 的情况下发布应用程序，则需要重新发布项目。 有关详细信息，请参阅[使用 Visual Studio 发布 Azure 云服务项目](http://go.microsoft.com/fwlink/p/?LinkId=623012)。

1. 当准备好部署 Azure 应用程序时，确认已将项目生成目标设置为“调试”。

1. 在“解决方案资源管理器”中右键单击项目，并从上下文菜单中选择“发布”。
   
1. 在“发布 Azure应用程序”对话框中，选择 Azure 订阅，并选择“下一步”。

1. 在“设置”页中，选择“高级设置”选项卡。

1. 打开“启用 IntelliTrace”选项，以在云中发布应用程序时为其收集 IntelliTrace 日志。
   
1. 若要自定义基本 IntelliTrace 配置，请选择“启用 IntelliTrace”旁边的“设置”。

    ![IntelliTrace 设置链接](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/intellitrace-settings-link.png)
   
1. 在“IntelliTrace 设置”对话框中，可以指定要记录的事件、是否收集调用信息、要收集其日志的模块和过程，以及分配给记录的空间量。 有关 IntelliTrace 的详细信息，请参阅[使用 IntelliTrace 进行调试](http://go.microsoft.com/fwlink/?LinkId=214468)。
   
    ![IntelliTrace 设置](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

IntelliTrace 日志是一个循环日志文件，IntelliTrace 设置中指定了该文件的最大大小（默认大小为 250 MB）。 IntelliTrace 日志会被收集到虚拟机的文件系统中的一个文件中。 当请求日志时，将及时拍摄该时刻的快照，并将该快照下载到本地计算机上。

将 Azure 云服务发布到 Azure 后，可以确定是否已从**服务器资源管理器**中的 Azure 节点启用了 IntelliTrace，如下图所示：

![服务器资源管理器 - 已启用 IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="download-intellitrace-logs-for-a-role-instance"></a>下载角色实例的 IntelliTrace 日志
使用 Visual Studio，可以通过执行以下步骤来下载角色实例的 IntelliTrace 日志：

1. 在“服务器资源管理器”中，展开“云服务”节点，并找到要下载其日志的角色实例。 

1. 右键单击此角色实例，并从上下文菜单中选择“查看 IntelliTrace 日志”。 

    ![“查看 IntelliTrace 日志”菜单选项](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/view-intellitrace-logs.png)

1. IntelliTrace 日志会被下载到本地计算机上某个目录中的文件中。 每次请求 IntelliTrace 日志时，就会创建新快照。 在下载日志时，Visual Studio 会在“Azure 活动日志”窗口中显示操作进度。 可以展开操作的行项以查看详细信息，如下图所示。

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

下载 IntelliTrace 日志时，可以在 Visual Studio 中继续工作。 日志下载完成后，会在 Visual Studio 中打开。

> [!NOTE]
> IntelliTrace 日志可能包含由框架生成并进行后续处理的异常。 如果这些异常时内部框架代码生成的，它们是启动角色的正常一环，可以安全地忽略。
> 
> 

## <a name="next-steps"></a>后续步骤
- [调试 Azure 云服务的选项](vs-azure-tools-debugging-cloud-services-overview.md)
- [使用 Visual Studio 发布 Azure 云服务](vs-azure-tools-publishing-a-cloud-service.md)