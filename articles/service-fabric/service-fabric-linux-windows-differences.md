---
title: "Linux 和 Windows 之间的 Azure Service Fabric 差异 | Microsoft Docs"
description: "Linux 上的 Azure Service Fabric 预览版和 Windows 上的 Azure Service Fabric 之间的差异。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: e155891ff8dc736e2f7de1b95f07ff7b2d5d4e1b
ms.openlocfilehash: 00c1f10f7df865344233143ce9cdf546e64acca0
ms.contentlocale: zh-cn
ms.lasthandoff: 05/02/2017


---
# <a name="differences-between-service-fabric-on-linux-preview-and-windows-generally-available"></a>Linux 上的 Service Fabric（预览版）与 Windows 上的 Service Fabric（正式版）之间的差异

由于 Linux 上的 Service Fabric 是预览版，因此某些在 Windows 上受支持的功能，在 Linux 上尚不受支持。 最终，当 Linux 上的 Service Fabric 公开发布时，这些功能集会一致。

* Linux 不支持 Reliable Collections（和 Reliable Stateful Services）。
* ReverseProxy 在 Linux 上不可用。
* 独立安装程序在 Linux 上不可用。
* 清单文件的 XML 架构验证不在 Linux 上执行。 
* Linux 不支持控制台重定向。 
* 故障分析服务 (FAS) 在 Linux 上不可用。
* Azure Active Directory 支持在 Linux 上不可用。
* Powershell 命令的某些 CLI 命令对等项不可用。
* 只能针对 Linux 群集运行部分 Powershell 命令（在下一部分展开介绍）。

>[!NOTE]
>即使在 Windows 上，也不支持在生产群集中进行控制台重定向。

Windows 和 Linux 的开发工具也不相同。 VisualStudio、Powershell、VSTS 和 ETW 用于 Windows，Yeoman、Eclipse、Jenkins 和 LTTng 用于 Linux。

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
* Cmd
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
* [使用 Azure CLI 管理 Service Fabric 应用程序](service-fabric-azure-cli.md)

