---
title: "对本地 Service Fabric 群集设置进行故障排除 | Microsoft 文档"
description: "本文就本地开发群集的故障介绍一些建议"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: mikkelhegn
ms.openlocfilehash: aa393f884b564cee81fcf75cc2eff895efea9471
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>排除本地开发群集安装的故障
如果在与本地 Azure Service Fabric 开发群集交互时遇到问题，请查看以下建议以获得可能的解决方案。

## <a name="cluster-setup-failures"></a>群集安装失败
### <a name="cannot-clean-up-service-fabric-logs"></a>无法清除 Service Fabric 日志
#### <a name="problem"></a>问题
在运行 DevClusterSetup 脚本时，看到了类似下面的错误：

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>解决方案
关闭当前 PowerShell 窗口，并以管理员身份打开一个新的 PowerShell 窗口。 现在应能成功运行该脚本。

## <a name="cluster-connection-failures"></a>群集连接失败
### <a name="service-fabric-powershell-cmdlets-are-not-recognized-in-azure-powershell"></a>Azure PowerShell 不能识别 Service Fabric PowerShell cmdlet
#### <a name="problem"></a>问题
如果尝试运行任何 Service Fabric PowerShell cmdlet（例如，在 Azure PowerShell 窗口中运行 `Connect-ServiceFabricCluster`），该操作会失败，指出无法识别该 cmdlet。 发生这种失败的原因是 Azure PowerShell 使用 32 位版本的 Windows PowerShell（即使在 64 位操作系统版本中），而 Service Fabric cmdlet 只能在 64 位环境中工作。

#### <a name="solution"></a>解决方案
始终直接从 Windows PowerShell 运行 Service Fabric cmdlet。

> [!NOTE]
> 最新版本的 Azure PowerShell 不创建特殊的快捷方式，因此不会再出现此问题。
> 
> 

### <a name="type-initialization-exception"></a>类型初始化异常
#### <a name="problem"></a>问题
在 PowerShell 中连接到群集时，会看到针对 System.Fabric.Common.AppTrace 的 TypeInitializationException 错误。

#### <a name="solution"></a>解决方案
在安装期间未正确设置路径变量。 从 Windows 注销并重新登录。 这将刷新你的路径。

### <a name="cluster-connection-fails-with-object-is-closed"></a>群集连接失败，并显示“对象已关闭”
#### <a name="problem"></a>问题
对 Connect-ServiceFabricCluster 的调用失败，并显示类似下面的错误：

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>解决方案
关闭当前 PowerShell 窗口，并以管理员身份打开一个新的 PowerShell 窗口。 现在应能成功连接。

### <a name="fabric-connection-denied-exception"></a>结构连接被拒绝异常
#### <a name="problem"></a>问题
从 Visual Studio 调试时，系统出现 FabricConnectionDeniedException 错误。

#### <a name="solution"></a>解决方案
此错误通常在手动启动一个服务主机进程（而不是允许 Service Fabric 运行时为你启动该进程）时出现。

确保在解决方案中没有将任何服务项目设置为启动项目。 只应将 Service Fabric 应用程序项目设置为启动项目。

> [!TIP]
> 如果本地群集在设置后开始出现异常行为，则可使用本地群集管理器系统托盘应用程序来重置它。 该操作将删除现有群集并设置一个新群集。 注意，所有部署的应用程序和关联的数据都会被删除。
> 
> 

## <a name="next-steps"></a>后续步骤
* [使用系统运行状况报告了解群集并排除故障](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [使用 Service Fabric Explorer 可视化群集](service-fabric-visualizing-your-cluster.md)

