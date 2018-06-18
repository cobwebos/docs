---
title: 升级 Azure Service Fabric 运行时 | Microsoft Docs
description: 本教程介绍如何使用 PowerShell 升级 Azure 托管的 Service Fabric 群集的运行时。
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2017
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: 375e0171f1a131ad95a9c6594cc0e05926e4dcc5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642829"
---
# <a name="tutorial-upgrade-the-runtime-of-a-service-fabric-cluster"></a>教程：升级 Service Fabric 群集的运行时

本教程是系列教程的第 3 部分，介绍如何升级 Azure Service Fabric 群集上的 Service Fabric 运行时。 本教程部分是针对 Azure 上运行的 Service Fabric 群集编写的，不适用于独立 Service Fabric 群集。

> [!WARNING]
> 本教程部分要求使用 PowerShell。 目前尚不支持使用 Azure CLI 工具升级群集运行时。 或者，可以在门户中升级群集。 有关详细信息，请参阅[升级 Azure Service Fabric 群集](service-fabric-cluster-upgrade.md)。

如果群集已在运行最新的 Service Fabric 运行时，则不需要执行此步骤。 但是，可以参考本文在 Azure Service Fabric 群集上安装任何受支持的运行时。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 读取群集版本
> * 设置群集版本

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * 使用模板在 Azure 上创建安全的 [Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)或 [Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
> * [缩小或扩大群集](service-fabric-tutorial-scale-cluster.md)
> * 升级群集的运行时
> * [部署 API 管理与 Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>先决条件
在开始学习本教程之前：
- 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 安装 [Azure PowerShell 模块 4.1 或更高版本](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)或 [Azure CLI 2.0](/cli/azure/install-azure-cli)。
- 在 Azure 上创建安全的 [Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)或 [Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
- 如果部署 Windows 群集，请设置 Windows 开发环境。 安装 [Visual Studio 2017](http://www.visualstudio.com) 和 **Azure 开发**、**ASP.NET 和 Web 开发**以及 **.NET Core 跨平台开发**工作负荷。  然后设置 [.NET 开发环境](service-fabric-get-started.md)。
- 如果部署 Linux 群集，请在 [Linux](service-fabric-get-started-linux.md) 或 [MacOS](service-fabric-get-started-mac.md) 上设置一个 Java 开发环境。  安装 [Service Fabric CLI](service-fabric-cli.md)。 

### <a name="sign-in-to-azure"></a>登录 Azure
执行 Azure 命令之前，登录到你的 Azure 帐户并选择你的订阅。

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>获取运行时版本

连接到 Azure 并选择包含 Service Fabric 群集的订阅之后，可获取群集的运行时版本。

```powershell
Get-AzureRmServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

或者，使用以下命令获取订阅中所有群集的列表：

```powershell
Get-AzureRmServiceFabricCluster | Select-Object Name, ClusterCodeVersion
```

请注意 **ClusterCodeVersion** 值。 在下一部分需使用此值。

## <a name="upgrade-the-runtime"></a>升级运行时

在 `Get-ServiceFabricRuntimeUpgradeVersion` cmdlet 中使用在上一部分获取的 **ClusterCodeVersion** 值来发现可升级到的版本。 只能在已连接到 Internet 的计算机上运行此 cmdlet。 例如，如果想要查看可从版本 `5.7.198.9494` 升级到哪些运行时版本，请使用以下命令：

```powershell
Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion "5.7.198.9494"
```

使用版本列表，可以让 Azure Service Fabric 群集升级到更新的运行时。 例如，如果可升级到版本 `6.0.219.9494`，请使用以下命令升级群集。

```powershell
Set-AzureRmServiceFabricUpgradeType -ResourceGroupName SFCLUSTERTUTORIALGROUP `
                                    -Name aztestcluster `
                                    -UpgradeMode Manual `
                                    -Version "6.0.219.9494"
```

> [!IMPORTANT]
> 群集运行时升级可能需要较长时间才能完成。 运行升级时，PowerShell 将被阻止。 可以使用另一个 PowerShell 会话来检查升级状态。

可以使用 PowerShell 或 Azure Service Fabric CLI (sfctl) 监视升级状态。

首先，请使用在本教程第一部分中创建的 SSL 证书连接到群集。 使用 `Connect-ServiceFabricCluster` cmdlet 或 `sfctl cluster upgrade-status`。

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
                             -KeepAliveIntervalInSec 10 `
                             -X509Credential -ServerCertThumbprint $thumbprint `
                             -FindType FindByThumbprint -FindValue $thumbprint `
                             -StoreLocation CurrentUser -StoreName My
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

接下来，使用 `Get-ServiceFabricClusterUpgrade` 或 `sfctl cluster upgrade-status` 显示状态。 将显示如下所示的结果。

```powershell
Get-ServiceFabricClusterUpgrade

TargetCodeVersion                          : 6.0.219.9494
TargetConfigVersion                        : 3
StartTimestampUtc                          : 11/28/2017 3:09:48 AM
UpgradeState                               : RollingForwardPending
UpgradeDuration                            : 00:09:00
CurrentUpgradeDomainDuration               : 00:09:00
NextUpgradeDomain                          : 1
UpgradeDomainsStatus                       : { "0" = "Completed";
                                             "1" = "Pending";
                                             "2" = "Pending";
                                             "3" = "Pending";
                                             "4" = "Pending" }
UpgradeKind                                : Rolling
RollingUpgradeMode                         : Monitored
FailureAction                              : Rollback
ForceRestart                               : False
UpgradeReplicaSetCheckTimeout              : 37201.09:59:01
HealthCheckWaitDuration                    : 00:05:00
HealthCheckStableDuration                  : 00:05:00
HealthCheckRetryTimeout                    : 00:45:00
UpgradeDomainTimeout                       : 02:00:00
UpgradeTimeout                             : 12:00:00
ConsiderWarningAsError                     : False
MaxPercentUnhealthyApplications            : 0
MaxPercentUnhealthyNodes                   : 100
ApplicationTypeHealthPolicyMap             : {}
EnableDeltaHealthEvaluation                : True
MaxPercentDeltaUnhealthyNodes              : 0
MaxPercentUpgradeDomainDeltaUnhealthyNodes : 0
ApplicationHealthPolicyMap                 : {}
```

```azurecli
sfctl cluster upgrade-status

{
  "codeVersion": "6.0.219.9494",
  "configVersion": "3",

... item cut to save space ...

  },
  "upgradeDomains": [
    {
      "name": "0",
      "state": "Completed"
    },
    {
      "name": "1",
      "state": "Pending"
    },
    {
      "name": "2",
      "state": "Pending"
    },
    {
      "name": "3",
      "state": "Pending"
    },
    {
      "name": "4",
      "state": "Pending"
    }
  ],
  "upgradeDurationInMilliseconds": "PT1H2M4.63889S",
  "upgradeState": "RollingForwardPending"
}
```

## <a name="conclusion"></a>结束语
本教程介绍了如何：

> [!div class="checklist"]
> * 获取群集运行时的版本
> * 升级群集运行时
> * 监视升级

接下来，转到以下教程了解如何使用 Service Fabric 群集部署 API 管理。
> [!div class="nextstepaction"]
> [部署 API 管理与 Service Fabric](service-fabric-tutorial-deploy-api-management.md)
