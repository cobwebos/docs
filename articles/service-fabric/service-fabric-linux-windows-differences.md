---
title: Linux 和 Windows 之间的 Azure Service Fabric 差异
description: Linux 上的 Azure Service Fabric 与 Windows 上的 Azure Service Fabric 之间的差异。
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: sfrev
ms.openlocfilehash: c064291491110b17c172a561afbd1e65d6c08d06
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748534"
---
# <a name="differences-between-service-fabric-on-linux-and-windows"></a>Linux 上的 Service Fabric 与 Windows 上的 Service Fabric 之间的差异

某些在 Windows 上受支持的功能，在 Linux 上尚不受支持。 最终，功能集将会一致，并且随着每个版本的发布，此功能差异将会缩小。 最新的可用版本之间存在以下差异。

* Envoy（反向代理）在 Linux 上以预览版提供
* 在 Linux 中尚未推出适用于 Linux 的独立安装程序
* 控制台重定向（在 Linux 或 Windows 生产群集中不受支持）
* Linux 上的故障分析服务 (FAS)
* 用于 Service Fabric 服务的 DNS 服务（Linux 上的容器支持 DNS 服务）
* 某些 Powershell 命令的 CLI 命令等效项（下面已列出，其中的大多数命令仅适用于独立群集）
* [可能影响可伸缩性的日志实现的差异](service-fabric-concepts-scalability.md#choosing-a-platform)

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
