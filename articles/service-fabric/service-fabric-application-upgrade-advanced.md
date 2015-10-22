<properties
   pageTitle="Service Fabric 应用程序升级：高级主题"
   description="本文介绍了与升级 Service Fabric 应用程序有关的一些高级主题。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="07/17/2015"
   wacn.date=""/>

# Service Fabric 应用程序升级：高级主题

## 手动升级模式

> [AZURE.NOTE]仅适用于失败或挂起的升级，甚至应考虑不受监控的手动模式。受监控的模式是建议用于 Service Fabric 应用程序的升级模式。

Service Fabric 提供了多个升级模式，可支持开发和生产群集。每个部署选项都非常适合不同的环境。受监控的应用程序滚动升级是生产中使用的最典型的升级。当指定了升级策略时，Service Fabric 可确保在升级继续进行之前，应用程序处于正常状态。在某些需要更多自定义或复杂运行状况评估策略的情况下，或者在非常规升级的情况下（应用程序已有数据丢失的情况等等），应用程序管理员可以使用手动应用程序滚动升级模式，以完全控制通过过个升级域进行升级的进度。最后，自动应用程序滚动升级对开发或测试环境很有用，可在服务开发期间提供快速迭代周期。

**手动** - 在当前 UD 停止应用程序升级，并将升级模式更改为不受监控的手动模式。管理员需要手动调用 **MoveNextApplicationUpgradeDomainAsync**，以继续进行升级或通过启动新升级触发回滚。升级进入到“手动”模式后，就会保持为“手动”模式，直到启动新的升级。**GetApplicationUpgradeProgressAsync** 命令将返回 FABRIC\_APPLICATION\_UPGRADE\_STATE\_ROLLING\_FORWARD\_PENDING。

## 使用差异包升级

Service Fabric 应用程序可以通过预配一个完整且独立的应用程序包进行升级。此外，还可以通过使用一个仅包含已更新应用程序文件、已更新应用程序清单和服务清单文件的差异包对应用程序进行升级。

完整的应用程序包中包含启动和运行 Service Fabric 应用程序所需的所有文件。差异包仅包含在最后一次设置与当前升级之间更改的文件，以及完整的应用程序清单和服务清单文件。如果应用程序清单或服务清单中存在任何 Service Fabric 无法在生成布局中找到的引用，则 Service Fabric 将在 ImageStore （链接 TBA）中搜索这些引用。

向群集首次安装应用程序时，需要完整的应用程序包。后续更新可以是完整的应用程序包或差异包。

在以下情况下，使用差异包将是一个不错的选择：

* 当你拥有一个引用了多个服务清单文件和/或多个代码包、配置包或数据包的大型应用程序包时，首选差异包。

* 当你的部署系统直接从应用程序生成过程产生生成布局时，首选差异包。在这种情况下，即使未对代码进行任何更改，新生成的程序集也将具有不同的校验和。使用完整的应用程序包需要你更新所有代码包上的版本。使用差异包时，你只提供更改的文件和其中的版本已更改的清单文件。

## 后续步骤

[升级教程](documentation/articles/service-fabric-application-upgrade-tutorial)

[升级参数](documentation/articles/service-fabric-application-upgrade-parameters)

[数据序列化](documentation/articles/service-fabric-application-upgrade-data-serialization)

[应用程序升级故障排除](documentation/articles/service-fabric-application-upgrade-troubleshooting)
 

<!---HONumber=74-->