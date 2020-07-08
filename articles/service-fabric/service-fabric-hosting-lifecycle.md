---
title: Azure Service Fabric 托管激活和停用生命周期
description: 说明节点上的应用程序和 ServicePackage 的生命周期
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: da395f020cf3d299211427e740975764653b487d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85801976"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Azure Service Fabric 托管生命周期
本文概述了在节点上激活应用程序时所发生的事件，以及用于控制该行为的各种群集配置。

在继续阅读之前，请确保了解[在 Service Fabric 中为应用程序建模][a1]中所述的各种概念及其相互关系。 

> [!NOTE]
> 在本文中，除非明确说明，否则：
>
> - *副本*指有状态服务的副本和无状态服务的实例。
> - *CodePackage*被视为等效于一个*ServiceHost*进程，该进程注册一个*Servicetype*，并承载该*servicetype*的服务副本。
>

## <a name="activation-of-applicationservicepackage"></a>激活应用程序/ServicePackage

用于激活的管道如下所示：

1. 下载 ApplicationPackage。 例如： ApplicationManifest.xml 等。
2. 为应用程序（ex：创建用户等）设置环境。
3. 开始跟踪应用程序以使其停用。
4. 下载 ServicePackage。 例如： ServiceManifest.xml、代码、配置、数据包等。
5. 设置用于服务包的环境 ex：设置防火墙、为终结点分配端口等。
6. 开始跟踪 ServicePackage 以便停用。
7. 启动 SetupEntryPoint Codepackage 并等待完成。
8. 开始 MainEntryPoint Codepackage。

### <a name="servicetype-blocklisting"></a>ServiceType 列入阻止列表
**ServiceTypeDisableFailureThreshold**确定了失败次数（激活、下载、CodePackage 故障），在此之后，会将 ServiceType 计划为列入阻止列表。 因此，第一次激活/下载失败或 CodePackage 崩溃应触发 ServiceType 列入阻止列表计划。 **ServiceTypeDisableGraceInterval** config 确定了在该节点上 ServiceType 最后标记为列入阻止列表的宽限时间间隔。 请注意，对于这种情况，激活/下载/CodePackage 重启应仍在内部重试模式，并通过托管子系统进行跟踪。 重试，例如： CodePackage 将计划在崩溃后再次启动，否则 Service Fabric 将再次尝试下载包。
列入阻止列表后，应该会看到 "ServiceTypeRegistration： ServiceType" 属性报告错误 "系统托管"。 在节点上禁用了 ServiceType。 "

将在节点上重新启用 ServiceType 
- 如果激活操作成功或在失败时达到**activationmaxfailurecount 次**的重试次数。
- 如果下载操作成功或在失败时达到**DeploymentMaxFailureCount**的重试次数。
- 如果损坏的 CodePackage 启动，则会重新启动并成功注册 ServiceType。

**Activationmaxfailurecount 次**DeploymentMaxFailureCount 重试后再次启用 ServiceType 的原因 / **DeploymentMaxFailureCount**是，它们是在节点上激活/下载应用程序 Service Fabric 将执行的最大尝试次数。 如果此操作未成功，则不会重试当前操作，因为 Service Fabric 要为服务提供另一个激活机会，这可能会成功，导致问题自动修复，它与激活/下载操作的生命周期相关联。 由于放置副本而触发的新激活/下载操作可能会再次触发 ServiceType 列入阻止列表，也可能会成功。

> [!NOTE]
> 如果 CodePackage 未注册 ServiceType，则不会影响 ServiceType。 只有承载副本崩溃的 CodePackage 才会影响 ServiceType。
>

### <a name="codepackage-crash"></a>CodePackage 崩溃
当 CodePackage 崩溃时，Service Fabric 将使用回退来重新启动它，而回退与代码包是否已向我们注册了类型无关。

回退值始终为 Min （RetryTime， **ActivationMaxRetryInterval**），该值可以基于**ActivationRetryBackoffExponentiationBase** config 为常量、线性或指数。

- 常量： If **ActivationRetryBackoffExponentiationBase** = = 0 then RetryTime = **ActivationRetryBackoffInterval**;
- 线性：如果**ActivationRetryBackoffExponentiationBase** = = 0，则 RetryTime = ContinuousFailureCount * **ActivationRetryBackoffInterval** ，其中 ContinousFailureCount 是 CodePackage 崩溃或无法激活的次数。
- 指数： RetryTime = （**ActivationRetryBackoffInterval** ） * （**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount）;
    
你可以根据需要控制 "快速重启" 的行为。 我们来谈谈线性。 这意味着，如果 CodePackage 崩溃，则启动间隔将在10、20和 30 40 秒之后，直到 CodePackage 被停用。 
    
在发生故障后 Service Fabric 关闭（等待）的最长时间量由**ActivationMaxRetryInterval**控制
    
如果 CodePackage 崩溃并重新启动，则需要保持**CodePackageContinuousExitFailureResetInterval**的 Service Fabric 将其视为正常，此时会将运行状况报告覆盖为正常，并重置 ContinousFailureCount。

### <a name="codepackage-not-registering-servicetype"></a>CodePackage 未注册 ServiceType
如果 CodePackage 保持活动状态，并且预计会向我们注册一个 ServiceType，但绝不会这样做，则在这种情况下 Service Fabric 将在**ServiceTypeRegistrationTimeout**指示 ServiceType 未在超时内配置时生成警告 HealthReport。

### <a name="activation-failure"></a>激活失败
如果在激活过程中发现错误，Service Fabric 始终使用线性回退（与 CodePackage 崩溃相同）。 这意味着激活操作将放弃（0 + 10 + 20 + 30 + 40） = 100 秒（第一次重试开始）。 不重试此激活后。
    
最大激活回退可以是**ActivationMaxRetryInterval**并重试**activationmaxfailurecount 次**。

### <a name="download-failure"></a>下载失败
如果在下载过程中遇到错误，Service Fabric 始终使用线性回退。 这意味着激活操作将放弃（0 + 10 + 20 + 30 + 40） = 100 秒（第一次重试开始）。 此下载不重试。 用于下载的线性回退等同于 ContinuousFailureCount ***DeploymentRetryBackoffInterval** ，可以最大为**DeploymentMaxRetryInterval**。 与激活一样，下载操作可以重试**activationmaxfailurecount 次**。

> [!NOTE]
> 在更改配置之前，需要注意以下几个示例。

* 如果 CodePackage 保持崩溃并退出，则将禁用 ServiceType。 但如果激活配置使它能够快速重新启动，则 CodePackage 可能会出现几次，然后才能看到 ServiceType 的禁用状态。 对于 ex：假定 CodePackage 出现，将 ServiceType 注册到 Service Fabric，然后崩溃。 在这种情况下，一旦宿主收到类型注册， **ServiceTypeDisableGraceInterval**时段就会被取消。 这会重复，直到 CodePackage 的值达到大于**ServiceTypeDisableGraceInterval**的值，然后才会在节点上禁用 ServiceType。 因此，可能需要一段时间，然后才能在节点上禁用 ServiceType。

* 在激活的情况下，当 Service Fabric 系统需要在节点上放置一个副本时，RA （ReconfigurationAgent）会要求承载子系统激活应用程序，并每15秒重试一次激活请求（**RAPMessageRetryInterval**）。 为了使 Service Fabric 系统知道是否已禁用 ServiceType，托管的激活操作需要的时间比重试时间间隔和**ServiceTypeDisableGraceInterval**长。 例如：让群集将配置**activationmaxfailurecount 次**设置为5，将**ActivationRetryBackoffInterval**设置为1秒。这意味着激活操作将放弃（0 + 1 + 2 + 3 + 4） = 10 秒（首次重试），并在该宿主放弃重试之后。 在这种情况下，激活操作将完成，并将在15秒后重试。 发生这种情况是因为 Service Fabric 在15秒内完成了所有重试。 因此，ReconfigurationAgent 中的每次重试都将在托管子系统中创建新的激活操作，该模式将保持重复，并且不会在该节点上禁用 ServiceType。 由于无法在节点 Sf 系统的组件调频（FailoverManager）上禁用 ServiceType，因此不会将副本移到其他节点。
> 

## <a name="deactivation"></a>停用

在节点上激活 ServicePackage 时，会对其进行跟踪以使其停用。 Deactivator 是跟踪它的实体。
Deactivator 的工作方式有两种：

1.  定期：在每个**DeactivationScanInterval**中，它将检查是否存在从未托管副本的 ServicePackages，并将其标记为候选项以便停用。
2.  ReplicaClose：如果副本已关闭，则 Deactivator 将获取 DecrementUsageCount。 如果计数为0，则表示 ServicePackage 不托管任何副本，因此是取消激活的候选项。

 基于激活模式的 "[独占/共享][a2]" 模式，在**DeactivationGraceInterval** for SharedMode/ **ExclusiveModeDeactivationGraceInterval** for ExclusiveMode 后计划用于停用的候选项。 如果在这段时间内有新的副本位置，则取消停用。

### <a name="periodically"></a>定期
示例1：假设 Deactivator 在时间 T （**DeactivationScanInterval**）进行扫描。 下一次扫描将位于2T。 假设 ServicePackage 激活发生在 T + 1 中。 此 ServicePackage 不承载副本，因此需要将其停用。 为了使 ServicePackage 成为停用的候选项，它需要处于 "无副本" 的状态（至少为 "T"）。 也就是说，它有资格在 2T + 1 上停用。 因此，2T 上的扫描将找不到此 ServicePackage 作为停用的候选项。 下一次停用周期3T 会将此 ServicePackage 计划为停用，因为现在它在没有时间 T 的副本状态。  

示例2：假设 ServicePackage 在时间 T-1 激活，Deactivator 将在 T 处扫描。ServicePackage 不承载副本。 然后在下一次扫描2T，此 ServicePackage 将被视为停用的候选项，因此将安排停用。  

示例3：假设 ServicePackage 在 T –1处激活，Deactivator 在 T 处进行扫描。ServicePackage 尚未托管副本。 现在，在 T + 1 上放置副本，即 宿主获取 IncrementUsageCount，这意味着将创建副本。 现在，在2T 中，此 ServicePackage 不会计划停用。 现在，停用会转到下面所述的 ReplicaClose 逻辑。

示例4：假设你的 ServicePackage 很大，如 10 GB，则在节点上下载可能需要一些时间。 激活应用程序后，Deactivator 将跟踪其生命周期。 现在，如果**DeactivationScanInterval**配置较小，则可能会遇到问题，因为在下载时，ServicePackage 不会在节点上激活。 若要解决此问题，可以[在节点上预下载 ServicePackage][p1]。 

> [!NOTE]
> 因此 ServicePackage 可以在任何位置（**DeactivationScanInterval**到 2 ***DeactivationScanInterval**） + **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**之间停用。 
>

### <a name="replica-close"></a>副本关闭：
Deactivator 保留 ServicePackage 所持有副本的计数。 如果 ServicePackage 持有副本并且副本关闭/关闭，则托管将获取 DecrementUsageCount。 打开副本时，承载将获取 IncrementUsageCount。 减量意味着 ServicePackage 现在承载一个较小的副本，并且当计数降到0时，ServicePackage 将被计划为停用，并且在该时间之后停用的时间为**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**。 

对于 ex：假设发生减量，并计划在 2T + X （**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**）停用 ServicePackage。 如果在这段时间内宿主获取一个 IncrementUsage，这意味着创建一个副本，则取消停用。

> [!NOTE]
>因此，这些配置基本上意味着： **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**：指定给 ServicePackage 的时间在托管任何副本后再次托管另一副本。 
**DeactivationScanInterval**：如果副本从未托管任何副本，则为 ServicePackage 指定的最短时间（即 如果未使用，则为。
>

### <a name="ctrlc"></a>Ctrl+C
ServicePackage 传递**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**并且仍未承载副本后，停用是非可取消。 CodePackage 是按 Ctrl + C 处理程序发出的，这意味着现在停用管道必须经过处理才能使进程停止运行。 在这段时间内，如果相同 ServicePackage 的新副本正在尝试执行，则会失败，因为我们无法从停用切换到激活。

## <a name="cluster-configs"></a>群集配置

具有默认值的配置会影响激活/decativation。

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold**：默认值为1。 失败计数的阈值，在此之后，会通知 FM （FailoverManager）禁用该节点上的服务类型，并尝试另一个节点进行放置。
**ServiceTypeDisableGraceInterval**：默认值为30秒。时间间隔过后，可以禁用服务类型。
**ServiceTypeRegistrationTimeout**：默认值为300秒。ServiceType 注册 Service Fabric 所用的超时时间。

### <a name="activation"></a>激活
**ActivationRetryBackoffInterval**：默认为每个激活失败的回退间隔。
**Activationmaxfailurecount 次**：默认值为20。 系统将在放弃之前重试失败的激活的最大计数。 
**ActivationRetryBackoffExponentiationBase**：默认1.5。
**ActivationMaxRetryInterval**：默认值为3600秒。发生故障时，不能再进行激活。
**CodePackageContinuousExitFailureResetInterval**：默认值为300秒。为 CodePackage 重置连续退出失败计数的超时时间。

### <a name="download"></a>下载
**DeploymentRetryBackoffInterval**：默认值为10。 部署失败的回退时间间隔。
**DeploymentMaxRetryInterval**：默认值为3600秒。失败时部署的最大备份数为默认值。
**DeploymentMaxFailureCount**：默认值为20。 重试 DeploymentMaxFailureCount 次应用程序部署后，节点上该应用程序的部署才会失败。

### <a name="deactivation"></a>停用
**DeactivationScanInterval**：默认值为600秒。如果某个副本从未托管任何副本，则为 ServicePackage 指定的最小时间（即 如果未使用，则为。
**DeactivationGraceInterval**：默认值为60秒。在**共享**进程模型的情况下，向 ServicePackage 托管其他副本的时间。
**ExclusiveModeDeactivationGraceInterval**：默认值为1秒。在**独占**进程模型托管任何副本后，给定给 ServicePackage 的时间再次托管另一个副本。

## <a name="next-steps"></a>后续步骤
[打包应用程序][a3]并准备好进行部署。

[部署和删除应用程序][a4]。 此文介绍如何使用 PowerShell 来管理应用程序实例。

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: https://docs.microsoft.com/powershell/module/servicefabric/copy-servicefabricservicepackagetonode
