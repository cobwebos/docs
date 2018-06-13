---
title: Linux 和 Windows 之间的 Azure Service Fabric 差异 | Microsoft Docs
description: Linux 上的 Azure Service Fabric 与 Windows 上的 Azure Service Fabric 之间的差异。
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 5e0002437b4fc22d9dfdcdca9a587bf420a7d8fa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207676"
---
# <a name="differences-between-service-fabric-on-linux-and-windows"></a>Linux 上的 Service Fabric 与 Windows 上的 Service Fabric 之间的差异

某些在 Windows 上受支持的功能，在 Linux 上尚不受支持。 最终，功能集将会一致，并且随着每个版本的发布，此功能差异将会缩小。 最新发行版（即 Windows 上的 6.0 版和 Linux 上的 6.0 版）之间存在以下差异： 

* 所有编程模型以预览版提供（Java/C# Reliable Actors、Stateless Services 和 Reliable Stateful Services）
* Envoy (ReverseProxy) 在 Linux 上以预览版提供
* 在 Linux 中尚未推出适用于 Linux 的独立安装程序
* 控制台重定向（在 Linux 或 Windows 生产群集中不受支持）
* Linux 上的故障分析服务 (FAS)
* 用于 Service Fabric 服务的 DNS 服务（Linux 上的容器支持 DNS 服务）
* 某些 Powershell 命令的 CLI 命令等效项（下面已列出，其中的大多数命令仅适用于独立群集）

Windows 和 Linux 的开发工具也不相同。 Visual Studio、Powershell、VSTS 和 ETW 用于 Windows，Yeoman、Eclipse、Jenkins 和 LTTng 用于 Linux。

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>不能对 Linux Service Fabric 群集使用的 Powershell cmdlet

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>后续步骤
* [在 Linux 上准备开发环境](service-fabric-get-started-linux.md)
* [在 OSX 上准备开发环境](service-fabric-get-started-mac.md)
* [使用 Yeoman 在 Linux 上创建和部署第一个 Service Fabric Java 应用程序](service-fabric-create-your-first-linux-application-with-java.md)
* [使用适用于 Eclipse 的 Service Fabric 插件在 Linux 上创建和部署第一个 Service Fabric Java 应用程序](service-fabric-get-started-eclipse.md)
* [在 Linux 上创建第一个 CSharp 应用程序](service-fabric-create-your-first-linux-application-with-csharp.md)
* [使用 Service Fabric CLI 管理应用程序](service-fabric-application-lifecycle-sfctl.md)
