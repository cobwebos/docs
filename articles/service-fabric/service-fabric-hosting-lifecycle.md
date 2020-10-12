---
title: Azure Service Fabric 宿主激活和停用生命周期
description: 介绍了节点上的应用程序和 ServicePackage 的生命周期
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: a39aecf16d1c3303c0a590b389ba2aa69d4472f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87405120"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Azure Service Fabric 宿主生命周期
本文概述了在节点上激活应用程序时发生的事件，以及用于控制该行为的各种群集配置。

在继续阅读之前，请确保了解[在 Service Fabric 中为应用程序建模][a1]中所述的各种概念及其相互关系。 

> [!NOTE]
> 在本文中，除非明确说明，否则：
>
> - “副本”指的是有状态服务的副本和无状态服务的实例。
> - CodePackage 被视为与注册 ServiceType 并承载该 ServiceType 的服务副本的 ServiceHost 进程相同。
>

## <a name="activation-of-applicationservicepackage"></a>激活 Application/ServicePackage

用于激活的管道如下所示：

1. 下载 ApplicationPackage。 例如：ApplicationManifest.xml 等。
2. 为 Application 设置环境（例如：创建用户等）。
3. 开始跟踪 Application 的停用情况。
4. 下载 ServicePackage。 例如：ServiceManifest.xml、代码、配置、数据包等。
5. 设置用于 ServicePackage 的环境，例如：设置防火墙、为终结点分配端口，等等。
6. 开始跟踪 ServicePackage 的停用情况。
7. 启动 Codepackage 的 SetupEntryPoint 并等待其完成。
8. 启动 Codepackage 的 MainEntryPoint。

### <a name="servicetype-blocklisting"></a>将 ServiceType 加入阻止列表
**ServiceTypeDisableFailureThreshold** 决定了在失败（激活、下载、CodePackage 失败）多少次后会安排将 ServiceType 加入阻止列表。 因此，第一次激活/下载失败或 CodePackage 崩溃就会触发将 ServiceType 加入阻止列表的安排。 **ServiceTypeDisableGraceInterval** 配置决定了在多长的宽限时间间隔过后就会在该节点上最终将 ServiceType 标记为已加入阻止列表。 请注意，这一切得以发生的前提是，激活/下载/CodePackage 重启应当仍在内部处于重试模式，并由宿主子系统进行跟踪。 举例来说，重试意味着：CodePackage 将安排在崩溃后再次启动，否则 Service Fabric 会再次尝试下载包。
加入阻止列表后，应该会显示错误“'System.Hosting' 针对属性 'ServiceTypeRegistration:ServiceType' 报告了错误。 ServiceType 已在节点上禁用。”

以下情况下，会在节点上重新启用 ServiceType： 
- 如果激活操作成功，或在失败后的重试次数达到 **ActivationMaxFailureCount** 次。
- 如果下载操作成功，或在失败后的重试次数达到 **DeploymentMaxFailureCount** 次。
- 如果已崩溃的 CodePackage 重新启动并成功注册 ServiceType。

之所以在 **ActivationMaxFailureCount**/**DeploymentMaxFailureCount** 次重试后会再次启用 ServiceType，是因为这是 Service Fabric 在节点上激活/下载应用程序之前会完成的最大尝试次数。 如果未成功，则不会重试当前操作。由于 Service Fabric 要为服务提供另一个激活机会，该激活可能会成功，这样就会自动修复问题，因此它与激活/下载操作的生命周期相关联。 由于放置副本而触发的新激活/下载操作可能会再次触发将 ServiceType 加入阻止列表的操作，也可能会成功。

> [!NOTE]
> 如果未注册 ServiceType 的 CodePackage 崩溃，则不会影响 ServiceType。 只有承载副本的 CodePackage 崩溃，才会影响 ServiceType。
>

### <a name="codepackage-crash"></a>CodePackage 崩溃
当 CodePackage 崩溃时，Service Fabric 将使用退避来重新启动它，该退避与代码包是否已向我们注册了类型无关。

退避值始终为 Min(RetryTime, **ActivationMaxRetryInterval**)。此值可以为常数、线性值或指数，具体取决于 **ActivationRetryBackoffExponentiationBase** 配置。

- 常数：如果 **ActivationRetryBackoffExponentiationBase** == 0，则 RetryTime = **ActivationRetryBackoffInterval**；
- 线性值：如果 **ActivationRetryBackoffExponentiationBase** == 0，则 RetryTime = ContinuousFailureCount* **ActivationRetryBackoffInterval**，其中的 ContinousFailureCount 是 CodePackage 崩溃或激活失败的次数。
- 指数：RetryTime = (**ActivationRetryBackoffInterval**，以秒为单位) * (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount)；
    
可以像快速重启那样根据需要控制此行为。 我们来谈谈线性值。 这意味着，如果 CodePackage 崩溃，则启动间隔将遵循 10、20、30、40 秒这样的模式，直到 CodePackage 被停用。 
    
故障后 Service Fabric 退避（等待）的最长时间是由 **ActivationMaxRetryInterval** 控制的
    
如果 CodePackage 崩溃并重新启动，它需要保持运行状态 **CodePackageContinuousExitFailureResetInterval** 秒的时间，然后 Service Fabric 才会将其视为正常运行，将运行状况报告重写为“正常”，并重置 ContinousFailureCount。

### <a name="codepackage-not-registering-servicetype"></a>CodePackage 未注册 ServiceType
如果 CodePackage 保持活动状态，并且预计会向我们注册一个 ServiceType，但却未这样做，则在这种情况下，Service Fabric 会在 **ServiceTypeRegistrationTimeout** 秒后生成一个警告 HealthReport，指出未在超时时间内配置 ServiceType。

### <a name="activation-failure"></a>激活失败
如果在激活过程中发现错误，Service Fabric 始终会使用线性退避（与 CodePackage 崩溃相同）。 这意味着在 (0+ 10 + 20 + 30 + 40) = 100 秒后会放弃激活操作（首次重试会立即进行）。 此后不重试激活。
    
最大激活退避可以是 **ActivationMaxRetryInterval** 秒，可以重试 **ActivationMaxFailureCount** 次。

### <a name="download-failure"></a>下载失败
如果在下载过程中遇到错误，Service Fabric 始终会使用线性退避。 这意味着在 (0+ 10 + 20 + 30 + 40) = 100 秒后会放弃激活操作（首次重试会立即进行）。 此后不重试下载。 下载的线性退避等于 ContinuousFailureCount***DeploymentRetryBackoffInterval**，最大退避可以为 **DeploymentMaxRetryInterval** 秒。 与激活一样，下载操作可以重试 **ActivationMaxFailureCount** 次。

> [!NOTE]
> 在更改配置之前，需要注意以下几个示例的情况。

* 如果 CodePackage 不断地崩溃并退出，则会禁用 ServiceType。 但如果激活配置允许它快速重启，则 CodePackage 可能会启动几次，然后才会看到 ServiceType 被禁用。 例如：假定 CodePackage 启动后将 ServiceType 注册到 Service Fabric，然后崩溃。 在这种情况下，一旦宿主收到类型注册，就会取消 **ServiceTypeDisableGraceInterval** 期限。 这种情况可能会重复下去，直到 CodePackage 退避到一个大于 **ServiceTypeDisableGraceInterval** 的值，然后系统才会在节点上禁用 ServiceType。 因此，可能需要过一段时间才会在节点上禁用 ServiceType。

* 在激活的情况下，当 Service Fabric 系统需要在节点上放置一个副本时，RA(ReconfigurationAgent) 会要求宿主子系统激活应用程序，并每隔 15 秒 (**RAPMessageRetryInterval**) 重试激活请求。 为了让 Service Fabric 系统知道是否已禁用 ServiceType，宿主中的激活操作需要的持续时间比重试间隔和 **ServiceTypeDisableGraceInterval** 长。 例如：让群集将配置 **ActivationMaxFailureCount** 设置为 5，将配置 **ActivationRetryBackoffInterval** 设置为 1 秒。这意味着激活操作会在 (0 + 1 + 2 + 3 + 4) = 10 秒后放弃（首次重试会立即进行），然后宿主会放弃重试。 在这种情况下，激活操作就完成了，不会在 15 秒后重试。 发生这种情况是因为 Service Fabric 在 15 秒内完成了所有重试。 因此，ReconfigurationAgent 中的每次重试都会在宿主子系统中创建新的激活操作。该模式会一直重复下去，不会在节点上禁用 ServiceType。 由于无法在节点上禁用 ServiceType，因此 Sf 系统的组件 FM(FailoverManager) 不会将副本移到其他节点。
> 

## <a name="deactivation"></a>停用

在节点上激活 ServicePackage 时，会跟踪其停用情况。 Deactivator 是跟踪它的实体。
Deactivator 的工作方式有两种：

1.  定期：每隔 **DeactivationScanInterval** 秒，它就会检查是否有从未承载过副本的 ServicePackage，并将其标记为要停用的候选项。
2.  ReplicaClose：如果副本已关闭，Deactivator 会获取 DecrementUsageCount。 如果计数为 0，则表明 ServicePackage 未承载任何副本，因此是停用的候选项。

 根据激活模式（[独占/共享][a2]），将会在 **DeactivationGraceInterval** 秒（适用于 SharedMode）/**ExclusiveModeDeactivationGraceInterval** 秒（适用于 ExclusiveMode）之后安排停用候选项。 如果在这段时间内进行了新的副本放置操作，则会取消停用。

### <a name="periodically"></a>定期：
示例1：假设 Deactivator 在执行 **扫描)  (** 。 其下次扫描的时间将是 2T。 假设 ServicePackage 激活发生在 T+1 秒的时间。 此 ServicePackage 不承载副本，因此需要将其停用。 ServicePackage 需要处于“无副本”状态至少 T 秒的时间才能成为停用的候选项。 也就是说，它会在 2T+1 秒的时候符合停用条件。 因此，2T 上的扫描将找不到此 ServicePackage 作为停用的候选项。 下一个停用周期 3T 会安排停用此 ServicePackage，因为此时它已处于“无副本”状态 T 秒长的时间。  

示例2：假设 ServicePackage 在时间 T-1 激活，Deactivator 将在 T 处扫描。ServicePackage 不承载副本。 那么，在 2T 秒的时候进行下一次扫描时，此 ServicePackage 会显示为停用候选项，因此系统会安排停用它。  

示例3：假设 ServicePackage 在 T –1处激活，Deactivator 在 T 处进行扫描。ServicePackage 尚未托管副本。 现在会在 T+1 秒的时候放置副本，也就是说， 宿主会获得一个 IncrementUsageCount，这意味着副本已创建。 现在，在 2T 秒的时候，系统不会安排停用此 ServicePackage。 现在，停用会转到下面所述的 ReplicaClose 逻辑。

示例 4：假设 ServicePackage 很大（例如 10 GB），则在节点上下载它可能需要一些时间。 激活应用程序后，Deactivator 会跟踪其生命周期。 现在，如果配置的 **DeactivationScanInterval** 值较小，则可能会遇到 ServicePackage 在节点上没有时间进行激活的问题，因为所有时间都已消耗在下载中。 若要解决此问题，可以[在节点上预先下载 ServicePackage][p1]。 

> [!NOTE]
> 这样一来，ServicePackage 就可以在 (**DeactivationScanInterval** 到 2***DeactivationScanInterval**) + **DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval** 的时间范围内停用。 
>

### <a name="replica-close"></a>副本关闭：
Deactivator 会保留 ServicePackage 保存的副本的计数。 如果 ServicePackage 保存了一个副本，而该副本处于关闭/故障状态，则宿主会获得一个 DecrementUsageCount。 打开副本时，宿主会获得一个 IncrementUsageCount。 Decrement（递减）意味着 ServicePackage 现在承载的副本数减一。当计数降到 0 时，系统就会安排停用 ServicePackage，在 **DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval** 秒过后停用它。 

对于 ex：假设发生减量，并计划在 2T + X (**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**) 上停用。 如果在这段时间内宿主获得了一个 IncrementUsage，则表明已创建一个副本，这种情况下会取消停用。

> [!NOTE]
>这些配置的大致含义：**DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval**：为 ServicePackage 留出的时间，在此时间内，允许其承载任意副本后再重新承载另一个副本。 
**DeactivationScanInterval**：为 ServicePackage 留出的最小时间，在此时间内，允许其在未承载任何副本（即从未使用过）的情况下 承载一个副本。
>

### <a name="ctrlc"></a>Ctrl+C
如果 ServicePackage 在经过 **DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval** 秒后仍未承载副本，则停用操作会变得不可取消。 系统会向 CodePackage 发出 Ctrl+C 处理程序，这意味着现在必须通过停用管道来停止进程。 在该时间内，如果尝试放置同一 ServicePackage 的新副本，则操作会失败，因为我们无法从“停用”转变为“激活”。

## <a name="cluster-configs"></a>群集配置

会影响激活/停用的配置及其默认值。

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold**：默认值为 1。 失败计数的阈值，超过此值就会通知 FM(FailoverManager) 禁用该节点上的此服务类型并尝试在另一个节点上放置对象。
**ServiceTypeDisableGraceInterval**：默认值为 30 秒。超过此时间间隔后就会禁用此服务类型。
**ServiceTypeRegistrationTimeout**：默认值为 300 秒。为 ServiceType 注册到 Service Fabric 设置的超时。

### <a name="activation"></a>激活
**ActivationRetryBackoffInterval**：默认值为 10 秒。每次激活失败时的退避间隔。
**ActivationMaxFailureCount**：默认值为 20。 系统在放弃之前重试失败激活的最大次数。 
**ActivationRetryBackoffExponentiationBase**：默认值为 1.5。
**ActivationMaxRetryInterval**：默认值为 3600 秒。失败时用于激活的最大退避值。
**CodePackageContinuousExitFailureResetInterval**：默认值为 300 秒。为 CodePackage 重置连续退出失败计数的超时时间。

### <a name="download"></a>下载
**DeploymentRetryBackoffInterval**：默认值为 10。 部署失败的回退时间间隔。
**DeploymentMaxRetryInterval**：默认值为 3600 秒。失败时用于部署的最大退避值。
**DeploymentMaxFailureCount**：默认值为 20。 重试 DeploymentMaxFailureCount 次应用程序部署后，节点上该应用程序的部署才会失败。

### <a name="deactivation"></a>停用
**DeactivationScanInterval**：默认值为 600 秒。为 ServicePackage 留出的最小时间，在此时间内，允许其在从未承载任何副本（即从未使用过）的情况下 承载一个副本。
**DeactivationGraceInterval**：默认值为 60 秒。在使用**共享**进程模型时，为 ServicePackage 留出的时间，在此时间内，允许其在承载任意副本后再重新承载另一个副本。
**ExclusiveModeDeactivationGraceInterval**：默认值为 1 秒。在使用**独占**进程模型时，为 ServicePackage 留出的时间，在此时间内，允许其在承载任意副本后再重新承载另一个副本。

## <a name="next-steps"></a>后续步骤
[打包应用程序][a3]并准备好进行部署。

[部署和删除应用程序][a4]。 此文介绍如何使用 PowerShell 来管理应用程序实例。

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
