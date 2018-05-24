---
title: 对本地 Azure Service Fabric 群集设置进行故障排除 | Microsoft Docs
description: 本文就本地开发群集的故障介绍一些建议
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: a7f58914fd6e498e717e19bfea11c9e3fcfc0399
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>排除本地开发群集安装的故障
如果在与本地 Azure Service Fabric 开发群集交互时遇到问题，请查看以下建议以获得可能的解决方案。

## <a name="cluster-setup-failures"></a>群集安装失败
### <a name="cannot-clean-up-service-fabric-logs"></a>无法清除 Service Fabric 日志
#### <a name="problem"></a>问题
在运行 DevClusterSetup 脚本时，可看到以下错误：

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>解决方案
关闭当前 PowerShell 窗口，并以管理员身份打开一个新的 PowerShell 窗口。 现在可成功运行脚本。

## <a name="cluster-connection-failures"></a>群集连接失败

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
关闭当前 PowerShell 窗口，并以管理员身份打开一个新的 PowerShell 窗口。

### <a name="fabric-connection-denied-exception"></a>结构连接被拒绝异常
#### <a name="problem"></a>问题
从 Visual Studio 调试时，系统出现 FabricConnectionDeniedException 错误。

#### <a name="solution"></a>解决方案
尝试手动启动服务主机进程时，通常会出现此错误。

确保在解决方案中没有将任何服务项目设置为启动项目。 只应将 Service Fabric 应用程序项目设置为启动项目。

> [!TIP]
> 如果本地群集在设置后开始出现异常行为，则可使用本地群集管理器系统托盘应用程序来重置它。 该操作将删除现有群集并设置一个新群集。 注意，所有部署的应用程序和关联的数据都会被删除。
> 
> 

## <a name="next-steps"></a>后续步骤
* [使用系统运行状况报告了解群集并排除故障](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [使用 Service Fabric Explorer 可视化群集](service-fabric-visualizing-your-cluster.md)

