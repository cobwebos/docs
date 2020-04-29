---
title: 高级应用程序升级主题
description: 本文介绍有关升级 Service Fabric 应用程序的一些高级主题。
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: a12d2ec55bda95c1c61d4a73c76f4a777f4237f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414500"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Service Fabric 应用程序升级：高级主题

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>在升级应用程序期间添加或删除服务类型

如果在升级过程中向已发布的应用程序添加新的服务类型，则该新的服务类型将添加到已部署的应用程序。 这样的升级不会影响已经是应用程序一部分的任何服务实例，但是，必须创建所添加的服务类型的实例，新的服务类型才会处于活动状态（请参阅 [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)）。

类似地，在升级过程中还可以从应用程序中删除服务类型。 但是，必须删除待删除服务类型的所有服务实例，然后才能继续进行升级（请参阅 [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)）。

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime"></a>避免无状态服务计划停机期间的连接中断

对于计划的无状态实例停机时间（例如应用程序/群集升级或节点停用），连接可能会被删除，因为在实例关闭后，会删除公开的终结点，从而导致强制连接闭包。

若要避免这种情况，请通过在服务配置中添加*实例关闭延迟持续时间*来配置*RequestDrain* （预览版）功能，以便在从群集内接收来自其他服务的请求时允许排出，并使用反向代理或将解析 API 与通知模型结合使用来更新终结点。 这可确保在关闭实例之前延迟启动*之前*，将删除无状态实例公布的终结点。 此延迟可使现有请求在实例实际关闭之前正常排空。 在启动延迟时回调函数会通知客户端终结点的更改，因此，它们可以重新解析终结点，并避免将新请求发送到即将关闭的实例。

### <a name="service-configuration"></a>服务配置

可通过多种方式在服务端配置延迟。

 * **创建新服务时**指定 `-InstanceCloseDelayDuration`：

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>`
    ```

 * **在应用程序清单的 defaults 节中定义服务**时分配 `InstanceCloseDelayDurationSeconds` 属性：

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **更新现有服务时**指定 `-InstanceCloseDelayDuration`：

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

### <a name="client-configuration"></a>客户端配置

若要在终结点发生更改时接收通知，客户端应注册回调，请参阅[ServiceNotificationFilterDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicenotificationfilterdescription)。
更改通知表示终结点已更改，客户端应重新解析终结点，而不使用不再播发的终结点，因为它们将很快关闭。

### <a name="optional-upgrade-overrides"></a>可选的升级替代方法

除了为每个服务设置默认延迟持续时间以外，还可以使用相同的 (`InstanceCloseDelayDurationSec`) 选项替代应用程序/群集升级期间的延迟：

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

延迟持续时间仅应用于调用的升级实例，而不会更改单个服务延迟配置。 例如，可以使用此方法指定 `0` 延迟，以跳过任何预配置的升级延迟。

> [!NOTE]
> 用于排出请求的设置不适用于来自 Azure 负载均衡器的请求。 如果调用服务使用基于投诉的解析，则此设置不起作用。
>
>

> [!NOTE]
> 当群集代码版本为7.1.XXX 或更高版本时，可以使用上述 Get-servicefabricservice cmdlet 在现有服务中配置此功能。
>
>

## <a name="manual-upgrade-mode"></a>手动升级模式

> [!NOTE]
> 对于所有 Service Fabric 升级，建议使用 *Monitored* 升级模式。
> 只有对于失败或暂停的升级，才应考虑使用 *UnmonitoredManual* 升级模式。 
>
>

在 *Monitored* 模式下，Service Fabric 应用运行状况策略来确保应用程序在升级过程中正常运行。 如果违反了运行状况策略，则升级将暂停或自动回滚，具体取决于指定的 *FailureAction*。

在 *UnmonitoredManual* 模式下，应用程序管理员对升级进度具有完全控制权。 当应用自定义运行状况评估策略或执行非常规升级来完全绕过运行状况监视时（例如，当应用程序已经丢失数据时），此模式非常有用。 在此模式下运行的升级在完成每个 UD 后会将其自己暂停，并且必须使用 [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) 显式使其继续执行。 当升级暂停并且就绪可供用户继续执行时，其升级状态将显示 *RollforwardPending*（请参阅 [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)）。

最后，*UnmonitoredAuto* 模式非常适用于在开发或或测试服务期间执行快速升级迭代，因为不需要提供用户输入并且不会评估应用程序运行状况策略。

## <a name="upgrade-with-a-diff-package"></a>使用差异包升级

还可以通过预配仅包含更新后的代码/配置/数据包以及完整的应用程序清单和完整的服务清单的差异包（不需要预配完整的应用程序包）来执行升级。 只有首次将应用程序安装到群集时，才需要完整的应用程序包。 后续升级可以使用完整的应用程序包或差异包执行。  

如果差异包的应用程序清单或服务清单中的任何引用无法在应用程序包中找到，则会自动将该引用替换为当前预配的版本。

使用差异包的方案有：

* 拥有一个引用了多个服务清单文件和/或多个代码包、配置包或数据包的大型应用程序包时。
* 当部署系统直接在应用程序生成过程中产生生成布局时。 在这种情况下，即使代码未发生任何更改，新生成的程序集也会获得不同的校验和。 若要使用完整的应用程序包，需要更新所有代码包上的版本。 使用差异包时，只提供更改的文件和其中的版本已更改的清单文件。

如果应用程序是使用 Visual Studio 升级的，则会自动发布差异包。 若要手动创建差异包，必须更新应用程序清单和服务清单，只在最终应用程序包中包含更改的包。

例如，让我们从以下应用程序开始（为便于理解，这里提供了版本号）：

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

假设你想要使用差异包仅更新 service1 的代码包。 更新后的应用程序有以下版本更改：

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

在本例中，你将应用程序清单更新为 2.0.0，并更新了 service1 的服务清单来反映代码包更新。 应用程序包的文件夹使用以下结构：

```text
app1/
  service1/
    code/
```

换句话说，就是照常创建完整的应用程序包，然后删除其版本未更改的任何代码/配置/数据包文件夹。

## <a name="upgrade-application-parameters-independently-of-version"></a>独立于版本升级应用程序参数

有时，需要更改 Service Fabric 应用程序的参数，而无需更改清单版本。 可以通过将 **-ApplicationParameter** 标志与 **Start-ServiceFabricApplicationUpgrade** 一起使用来方便地完成此操作。 假设 Service Fabric 应用程序具有以下属性：

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

现在使用 **Start-ServiceFabricApplicationUpgrade** cmdlet 升级该应用程序。 此示例显示了受监视的升级，但也可以使用不受监视的升级。 若要查看此 cmdlet 接受的标志的完整说明，请参阅 [Azure Service Fabric PowerShell 模块参考](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters)

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

升级后，确认应用程序具有已更新的参数和相同的版本：

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>回滚应用程序升级

虽然升级可以通过三种模式之一（*Monitored*、*UnmonitoredAuto* 或 *UnmonitoredManual*）进行前滚，但它们只能在 *UnmonitoredAuto* 或 *UnmonitoredManual* 模式下进行回滚。 在 *UnmonitoredAuto* 模式下进行回滚与进行前滚时行为相同，唯一的例外是 *UpgradeReplicaSetCheckTimeout* 的默认值不同 - 请参阅[应用程序升级参数](service-fabric-application-upgrade-parameters.md)。 在 *UnmonitoredManual* 模式下进行回滚与进行前滚时行为相同 - 回滚在完成每个 UD 后会将其自己暂停，并且必须使用 [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) 显式继续执行回滚。

当违反了在 *Monitored* 模式下进行的升级的运行状况策略并且 *FailureAction* 为 *Rollback* 时，可以自动触发回滚（请参阅[应用程序升级参数](service-fabric-application-upgrade-parameters.md)）；也可以使用 [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps) 显式触发回滚。

在回滚期间，可以随时使用 [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps) 更改 *UpgradeReplicaSetCheckTimeout* 的值和模式。

## <a name="next-steps"></a>后续步骤
[使用 Visual Studio 升级应用程序](service-fabric-application-upgrade-tutorial.md)逐步讲解了如何使用 Visual Studio 进行应用程序升级。

[使用 Powershell 升级应用程序](service-fabric-application-upgrade-tutorial-powershell.md)逐步讲解如何使用 powershell 进行应用程序升级。

使用[升级参数](service-fabric-application-upgrade-parameters.md)来控制应用程序的升级方式。

了解如何使用[数据序列化](service-fabric-application-upgrade-data-serialization.md)，使应用程序在升级后保持兼容。

参考[对应用程序升级进行故障排除](service-fabric-application-upgrade-troubleshooting.md)中的步骤来解决应用程序升级时的常见问题。
