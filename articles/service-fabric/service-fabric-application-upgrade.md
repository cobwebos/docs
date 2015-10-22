<properties
    pageTitle="Service Fabric 应用程序升级"
    description="本文简单介绍了 Service Fabric 应用程序的升级。"
    services="service-fabric"
    documentationCenter=".net"
    authors="mani-ramaswamy"
    manager="samgeo"
    editor=""/>

<tags
    ms.service="service-fabric"
    ms.date="07/17/2015"
    wacn.date=""/>


# Service Fabric 应用程序升级


Service Fabric 应用程序是多个服务的集合。在升级期间，Service Fabric 将新的[应用程序清单](documentation/articles/service-fabric-application-model#describe-an-application)与以前的版本进行比较，并确定应用程序中的哪些服务需要升级。这通过将服务清单中的版本号与之前的版本号进行比较来确定。如果服务未更改，则不升级服务。

## 滚动升级概述
在应用程序滚动升级过程中，分阶段进行升级。在每个阶段，对群集中的部分节点进行升级，这一部分节点称为升级域。因此，应用程序在整个升级过程中保持可用。升级期间，群集中可能混合了新旧版本。因此，两个版本均必须可向前和向后兼容。如果它们不兼容，则由应用程序管理员负责分阶段进行多阶段升级，以保持可用性。实现此操作的方法是先使用与先前版本兼容的应用程序中间版本进行升级，然后再升级到最终版本。

在配置群集时在群集清单中指定升级域。不应假定升级域将按特定顺序接收更新。升级域是应用程序部署的逻辑单元。升级域可让服务在升级过程中保持高可用性。

如果对群集中的所有节点应用升级，即应用程序只有一个升级域，将不可能进行任何滚动升级。由于服务将处于关闭状态，并且在升级时不可用，因此不建议这样做。此外，当仅为群集设置了一个升级域时，Azure 不提供任何保证。

## 升级过程中的运行状况检查
必须对升级设置运行状况策略（或者可使用默认值）。当所有升级域均在指定的超时内进行了升级，并且所有升级域均被认为运行正常时，即可称升级为成功升级。正常升级域意味着升级域通过了运行状况策略中指定的所有运行状况检查，例如运行状况策略可能要求应用程序实例中的所有服务必须都<em>运行正常</em>，运行状况由 Service Fabric 定义。

升级期间 Service Fabric 执行的运行状况策略和检查与服务和应用程序无关。也就是说，不检查任何特定于服务的测试。例如，服务具有最小吞吐量要求。但 Service Fabric 没有对其进行测试的信息，因此将不会检查为应用程序定义的吞吐量。请参考[运行状况文章](documentation/articles/service-fabric-health-introduction)，了解执行的检查 - 升级过程中的检查包括是否正确复制了应用程序包、是否已启动实例等等。

应用程序运行状况是应用程序子实体的聚合。简而言之，Service Fabric 通过报告的应用程序运行状况以及应用程序服务的所有运行状况来评估应用程序的运行状况。通过聚合子实体（如服务副本）的运行状况进一步评估应用程序服务的运行状况。一旦满足应用程序运行状况策略，升级即可继续；如果违反运行状况策略，应用程序升级即告失败。

## 升级模式

升级的常见模式（和建议的模式）为受监控的升级。受监控的升级对一个升级域执行升级，如果所有运行状况检查均通过（按指定的策略），则自动移动到下一个升级域，依此类推。如果运行状况检查失败和/或达到超时，则升级针对升级域回滚，或者模式更改为 UnmonitoredManual（如果在升级时选择了该选项）。

UnmonitoredManual 在每次对一个升级域进行升级后都需要手动干预，以开始对下一个升级域进行升级。在开始下一个升级域的升级之前，不存在已执行的 Service Fabric 运行状况检查，而是由干预者确定要执行的运行状况检查或状态检查。

## 应用程序升级流程图

下面的流程图有助于理解 Service Fabric 应用程序的升级过程。具体而言，该流程描述当一个升级域的升级被认为成功或失败时，超时（包括 *HealthCheckStableDuration*、*HealthCheckRetryTimeout* 和 *UpgradeHealthCheckInterval*）如何为控制提供帮助。

![Service Fabric 应用程序的升级过程][image]


## 后续步骤

[升级教程](documentation/articles/service-fabric-application-upgrade-tutorial)

[升级参数](documentation/articles/service-fabric-application-upgrade-parameters)

[数据序列化](documentation/articles/service-fabric-application-upgrade-data-serialization)

[高级主题](documentation/articles/service-fabric-application-upgrade-advanced)

[应用程序升级故障排除](documentation/articles/service-fabric-application-upgrade-troubleshooting)



[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
 

<!---HONumber=74-->