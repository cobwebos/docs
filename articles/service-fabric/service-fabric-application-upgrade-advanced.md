<properties
   pageTitle="应用程序升级：高级主题 | Microsoft Azure"
   description="本文介绍了与升级 Service Fabric 应用程序有关的一些高级主题。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="02/04/2016"
   wacn.date=""/>

# Service Fabric 应用程序升级：高级主题

## 手动升级模式

> [AZURE.NOTE]  只应针对已失败或暂停的升级考虑使用不受监视的手动模式。受监视模式是建议用于 Service Fabric 应用程序的升级模式。

Azure Service Fabric 提供了多个升级模式，可支持开发和生产群集。每个部署选项都非常适合不同的环境。

受监视应用程序滚动升级是生产中使用的最典型的升级。当指定了升级策略时，Service Fabric 可确保在升级继续进行之前，应用程序处于正常状态。

 应用程序管理员可以使用手动滚动应用程序升级模式，通过各种升级域获得升级过程的完全控制权。对于某些需要自定义程度更高或更复杂的运行状况评估策略的情况，或者如果存在非常规升级（例如，应用程序已有数据丢失），此模式非常有用。

最后，自动应用程序滚动升级对开发或测试环境很有用，可在服务开发期间提供快速迭代周期。

## 切换到手动升级模式
**手动** - 在当前 UD 停止应用程序升级，并将升级模式更改为不受监视的手动模式。管理员需要手动调用 **MoveNextApplicationUpgradeDomainAsync**，以继续进行升级或通过启动新升级触发回滚。升级进入到“手动”模式后，就会保持为“手动”模式，直到启动新的升级。**GetApplicationUpgradeProgressAsync** 命令将返回 FABRIC\\APPLICATION\\UPGRADE\\STATE\\ROLLING\\FORWARD\\PENDING。

## 使用差异包升级

Service Fabric 应用程序可以通过预配一个完整且独立的应用程序包进行升级。此外，还可以通过使用一个仅包含已更新应用程序文件、已更新应用程序清单和服务清单文件的差异包对应用程序进行升级。

完整的应用程序包中包含启动和运行 Service Fabric 应用程序所需的所有文件。差异包仅包含在最后一次设置与当前升级之间更改的文件，以及完整的应用程序清单和服务清单文件。如果应用程序清单或服务清单中存在任何 Service Fabric 无法在生成布局中找到的引用，则 Service Fabric 将在映像存储中搜索。

向群集首次安装应用程序时，需要完整的应用程序包。后续更新可以是完整的应用程序包或差异包。

在以下情况下，使用差异包将是一个不错的选择：

* 当你拥有一个引用了多个服务清单文件和/或多个代码包、配置包或数据包的大型应用程序包时，首选差异包。

* 当你的部署系统直接从应用程序生成过程产生生成布局时，首选差异包。在这种情况下，即使未对代码进行任何更改，新生成的程序集也将具有不同的校验和。使用完整的应用程序包需要你更新所有代码包上的版本。使用差异包时，你只提供更改的文件和其中的版本已更改的清单文件。

## 后续步骤

[使用 Visual Studio 升级应用程序](/documentation/articles/service-fabric-application-upgrade-tutorial)将逐步指导你使用 Visual Studio 进行应用程序升级。

[使用 PowerShell 升级应用程序](/documentation/articles/service-fabric-application-upgrade-tutorial-powershell)将逐步指导你使用 PowerShell 进行应用程序升级。
使用[升级参数](/documentation/articles/service-fabric-application-upgrade-parameters)来控制应用程序的升级方式。

了解如何使用[数据序列化](/documentation/articles/service-fabric-application-upgrade-data-serialization)，使应用程序在升级后保持兼容。

参考[对应用程序升级进行故障排除](/documentation/articles/service-fabric-application-upgrade-troubleshooting)中的步骤来解决应用程序升级时的常见问题。
 

<!---HONumber=Mooncake_0307_2016-->