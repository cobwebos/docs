---
title: "Azure Service Fabric 修补业务流程应用程序 | Microsoft Docs"
description: "用于在 Service Fabric 群集中自动修补 OS 的应用程序。"
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: 
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/9/2017
ms.author: nachandr
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: a3554881e7db894c602e73feb385b5b361837409
ms.contentlocale: zh-cn
ms.lasthandoff: 05/18/2017


---

# <a name="application-to-patch-windows-os-in-your-service-fabric-cluster"></a>用于在 Service Fabric 群集中修补 Windows OS 的应用程序

修补业务流程应用程序是一个 Service Fabric 应用程序，可以自动修补 Azure 中或本地 Service Fabric 群集上的 OS，而无需停机。

修补业务流程应用程序包括以下突出功能：

1. **自动 OS 更新安装**：修补业务流程应用程序可确保自动下载和安装更新，并在适当的情况下重新启动节点。
    无需停机即可在所有群集节点上完成此操作。

1. **群集感知的修补和运行状况集成**：修补业务流程应用程序在应用更新的同时，可在群集运行过程中对其进行监视，每次更新一个节点或者一个升级域。 
    只要它检测到由于修补过程问题而导致群集运行状况变差，就会停止修补过程，防止问题恶化。

1. **支持所有 Service Fabric 群集**：该应用程序的兼容性足够高，可在 Azure Service Fabric 群集和独立群集中工作。
    > [!NOTE]
    > 对独立群集的支持即将推出。

## <a name="link-to-download-the-application-package"></a>用于下载应用程序包的链接

从[下载链接](https://go.microsoft.com/fwlink/P/?linkid=849590)下载该应用程序

## <a name="internal-details-of-the-application"></a>应用程序的内部详细信息

修补业务流程应用程序由以下子组件构成：

- **协调器服务**：一个有状态服务。 该服务负责
    - 协调整个群集上的 Windows 更新作业。
    - 存储已完成的 Windows 更新操作的结果。
- **节点代理服务**：一个无状态服务，在所有 Service Fabric 群集节点上运行。 该服务负责
    - 引导节点代理 NTService。
    - 监视节点代理 NTService。
- **节点代理 NTService**：一个 Windows NT 服务。 节点代理 NTService 以较高的特权 (SYSTEM) 运行。 相比之下，节点代理服务和协调器服务以较低级别的特权 (NETWORK SERVICE) 运行。 该服务负责在所有群集节点上执行以下 Windows 更新作业。
    - 在节点上禁用自动 Windows 更新。
    - 根据用户提供的策略下载和安装 Windows 更新。
    - 安装 Windows 更新后重新启动计算机
    - 将 Windows 更新的结果上传到协调器服务。
    - 操作失败时，重试次数用完后报告运行状况。

> [!NOTE]
> 修补业务流程应用程序使用 Service Fabric 系统服务“修复管理器”来禁用/启用节点和执行运行状况检查。 修补业务流程应用程序创建的修复任务跟踪每个节点的 Windows 更新进度。

## <a name="prerequisites-for-using-the-application"></a>使用该应用程序需满足的先决条件

### <a name="ensure-service-fabric-version-is-55-or-above"></a>确保 Service Fabric 版本为 5.5 或以上

可以在装有 Service Fabric 运行时 v5.5 或更高版本的群集上运行修补业务流程应用程序。

### <a name="enable-repair-manager-service-if-not-running-already"></a>启用修复管理器服务（如果尚未运行）

修补业务流程应用程序需要在群集上启用修复管理器系统服务。

#### <a name="service-fabric-clusters-on-azure"></a>Azure 上 的 Service Fabric 群集

银级持久性层中的 Azure 群集默认已启用修复管理器。 金级持久性层中的 Azure 群集不一定已启用修复管理器，具体取决于这些群集是多久前创建的。 铜级持久性层中的 Azure 群集默认未启用修复管理器服务。 

如果在 Service Fabric 资源管理器中的系统服务部分下面未看到修复管理器服务运行，可以使用 [Azure Resource Manager 模板](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)在新的/现有的 Service Fabric 群集上启用该服务。

首先，请获取要部署的群集的模板。 可以使用示例模板，或者创建自定义 Resource Manager 模板。 然后，可使用以下步骤启用修复管理器：

1. 首先，检查 `apiversion` 是否针对 `Microsoft.ServiceFabric/clusters` 资源设置为 `2017-07-01-preview`，如以下代码片段所示。 如果不同，需要将 `apiVersion` 更新为值 `2017-07-01-preview`

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 现在，通过在 `fabricSettings` 节后面添加以下 `addonFeaturres` 节来启用修复管理器服务，如下所示

    ```json
    "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "RepairManager"
        ],
    ```

3. 通过这些更改更新群集模板后，应用更改并等待升级完成。 完成后，现在可看到修复管理器系统服务在群集中运行，该服务在 Service Fabric 资源管理器中的系统服务部分下称为 `fabric:/System/RepairManagerService`。 

#### <a name="standalone-on-premise-clusters"></a>独立本地群集

> [!NOTE]
> 对独立群集的支持即将推出

### <a name="disable-automatic-windows-update-on-all-nodes"></a>在所有节点上禁用自动 Windows 更新。

在 Service Fabric 群集上启用自动 Windows 更新可能导致失去可用性，因为多个节点可能同时重新启动以完成更新。

修补业务流程应用程序默认会尝试在每个群集节点上禁用自动 Windows 更新。

但是，如果设置由管理员/组策略管理，我们建议显式将 Windows 更新策略设置为“下载之前发出通知”。


### <a name="optional-enable-windows-azure-diagnostics"></a>可选：启用 Windows Azure 诊断

在接下来的几天，将会收集修补业务流程应用程序的日志作为 Service Fabric 日志本身的一部分。 但是，只有在完成该过程后，才会在本地为每个群集节点收集日志。 我们建议将 Windows Azure 诊断 (WAD) 配置为将日志从所有节点上传到一个中心位置。

[此文](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-how-to-setup-wad)详细介绍了有关启用 WAD 的步骤。

将会针对以下固定提供程序 ID 生成修补业务流程应用程序的日志。

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

在 Azure Resource Manager 模板中的“WadCfg”节中添加以下节： 

```json
"PatchOrchestrationApplication": \[
  {
    "provider": "e39b723c-590c-4090-abb0-11e3e6616346",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
      "eventDestination": "PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "fc0028ff-bfdc-499f-80dc-ed922c52c5e9",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "24afa313-0d3b-4c7c-b485-1047fd964b60",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "05dc046c-60e9-4ef7-965e-91660adffa68",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
\]
```

> [!NOTE]
> 如果 Service Fabric 群集由多个节点类型构成，则必须为所有“WadCfg”节添加上述节。

## <a name="configuring-the-application"></a>配置应用程序

用户可以根据需要设置以下配置，以调整修补业务流程应用程序的行为。

在创建/更新应用程序的过程中传入应用程序参数可以替代默认值。 可以通过在 cmdlet `Start-ServiceFabricApplicationUpgrade` 或 `New-ServiceFabricApplication` 中指定 `ApplicationParameter` 来提供应用程序参数

|**Parameter**        |**类型**                          | **详细信息**|
|:-|-|-|
|MaxResultsToCache    |Long                              | 应缓存的 Windows 更新结果历史记录数上限。 <br>默认值为 3000（假设） <br> - 节点数为 20 <br> - 节点上每月发生的更新次数为 5 <br> - 每个操作的结果数可为 10 <br> - 应存储过去 3 个月的结果 |
|TaskApprovalPolicy   |枚举 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy 指示 CoordinatorService 用于在 Service Fabric 群集节点上安装 Windows 更新的策略<br>                         允许值包括： <br><br>                                                           <b>NodeWise</b> - 每次在一个节点上安装 Windows 更新 <br>                                                           <b>UpgradeDomainWise</b> - 每次在一个升级域上安装 Windows 更新（属于一个升级域的所有节点都可以安装 Windows 更新）
|LogsDiskQuotaInMB   |Long  <br> （默认值：1024）               |可在节点本地保存的修补业务流程应用程序日志的最大大小，以 MB 为单位
| WUQuery               | 字符串<br>（默认值："IsInstalled=0"）                | 查询以获取 Windows 更新，有关详细信息，请参阅 [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)。
| InstallWindowsOSOnlyUpdates | Bool <br> （默认值：True）                 | 此标志用于安装 Windows OS 更新。            |
| WUOperationTimeOutInMinutes | int <br>（默认值：90）                   | 指定任何 Windows 更新操作（搜索/下载/安装）的超时。 在指定的超时内未完成的操作将被中止。       |
| WURescheduleCount     | int <br> （默认值：5）                  | 此配置确定在操作持续失败的情况下，服务重新计划 Windows 更新的最大次数          |
| WURescheduleTimeInMinutes | int <br>（默认值：30） | 此配置确定在持续失败的情况下，服务重新计划 Windows 更新的间隔 |
| WUFrequency           | 逗号分隔的字符串（默认值："Weekly, Wednesday, 7:00:00"）     | 安装 Windows 更新的频率。 格式和可能值如下 <br>-   Monthly,DD,HH:MM:SS Ex: Monthly,5,12:22:32 <br> -   Weekly,DAY,HH:MM:SS Ex: Weekly, Tuesday, 12:22:32  <br> -   Daily, HH:MM:SS Ex: Daily, 12:22:32  <br> - None - 表示不应执行 Windows 更新  <br><br> 注意：所有时间采用 UTC 格式|
| AcceptWindowsUpdateEula | Bool <br>（默认值：True） | 如果设置此标志，应用程序将代表计算机的所有者接受 Windows 更新的 EULA。              |


## <a name="steps-to-deploy-the-application"></a>部署应用程序的部署

1. 完成所有必备步骤来准备群集。
1. 部署应用程序 - 可以使用[此处](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)所述的步骤通过 PowerShell 完成。
1. 若要在部署时配置应用程序，请在 cmdlet `New-ServiceFabricApplication` 中使用 `ApplicationParamater`

    为了方便用户，我们连同应用程序一起提供了脚本 Deploy.ps1。 使用方式：

    - 使用 `Connect-ServiceFabricCluster` 连接到 Service Fabric 群集
    - 结合相应的 `ApplicationParameter` 值执行 PowerShell 脚本 Deploy.ps1

> [!NOTE]
> 将脚本和应用程序文件夹 PatchOrchestrationApplication 保存在同一目录中。

## <a name="steps-to-upgrade-the-application"></a>升级应用程序的部署

若要使用 PowerShell 升级现有的修补业务流程应用程序，请参阅[此文](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)所述的应用程序升级步骤。

如果只想要更改应用程序的应用程序参数，请在现有的应用程序版本中为 cmdlet `Start-ServiceFabricApplicationUpgrade` 提供 `ApplicationParamater`

## <a name="steps-to-remove-the-application"></a>删除应用程序的部署

若要删除应用程序，请参阅[此文](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)所述的步骤。

为了方便用户，我们连同应用程序一起提供了脚本 Undeploy.ps1。 使用方式：
    - 使用 ```Connect-ServiceFabricCluster``` 连接到 Service Fabric 群集
    - 执行 PowerShell 脚本 Undeploy.ps1

> [!NOTE]
> 将脚本和应用程序文件夹 PatchOrchestrationApplication 保存在同一目录中。

## <a name="viewing-the-windows-update-results"></a>查看 Windows 更新结果

修补业务流程应用程序公开了 REST API 用于向用户显示历史结果。

若要查询 Windows 更新结果，可以登录到群集，找到协调器服务的主副本地址，然后在浏览器中点击该 URL。
http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults。CoordinatorService 的 REST 终结点提供了动态端口用于检查对 ServiceFabric 资源管理器的确切 URL 引用。
例如：在以下示例中，结果将在 `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`
![REST 终结点的映像](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)中提供


如果在群集上启用了反向代理，则用户也可以从群集外部访问该 URL。
需要访问的终结点：http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults。可以遵循[此文](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)所述的步骤在群集上启用反向代理。 

> 
> [!WARNING]
> 配置反向代理后，可从群集外部访问群集中公开 HTTP 终结点的所有微服务。

## <a name="diagnostics--health-events"></a>诊断/运行状况事件

### <a name="collecting-patch-orchestration-application-logs"></a>收集修补业务流程应用程序日志

在接下来的几天，将会收集修补业务流程应用程序日志作为 Service Fabric 日志的一部分。
在此之前，可以使用以下方法之一收集日志

#### <a name="locally-on-each-node"></a>在每个节点本地

在每个 Service Fabric 群集节点本地收集日志。 日志的访问位置为 \[Service Fabric\_Installation\_Drive\]:\\PatchOrchestrationApplication\\logs

例如：如果 Service Fabric 安装在“D”驱动器上，则路径为 D:\\PatchOrchestrationApplication\\logs

#### <a name="central-location"></a>中心位置

如果在执行必备步骤期间配置了 WAD，则 Azure 存储中将会提供修补业务流程应用程序的日志。

### <a name="health-reports"></a>运行状况报告

对于以下情况下，修补业务流程应用程序还会针对 CoordinatorService 或 NodeAgentService 发布运行状况报告

#### <a name="windows-update-operation-failed"></a>Windows 更新操作失败

如果某个节点上的 Windows 更新操作失败，将会针对 NodeAgentService 生成运行状况报告。
运行状况报告的详细信息将包含有问题的节点名称。

在有问题的节点上成功完成修补后，将自动清除该报告。

#### <a name="node-agent-ntservice-is-down"></a>节点代理 NTService 已关闭

如果节点上的 NodeAgentNTService 已关闭，将会针对 NodeAgentService 生成警告级别的运行状况报告

#### <a name="repair-manager-is-not-enabled"></a>修复管理器未启用

如果在群集上找不到修复管理器服务，将会针对 CoordinatorService 生成警告级别的运行状况报告。

## <a name="frequently-asked-questions"></a>常见问题解答：

问： **修补业务流程应用程序正在运行时，我发现群集处于错误状态**。

A. 在安装过程中，修补业务流程应用程序将会禁用和/或重新启动节点，这可能会暂时导致群集停止运行状况变差。

根据应用程序的策略，执行修补操作期间可以有一个节点关闭，或者整个升级域同时关闭。

请务必注意，在 Windows 更新安装结束时，重新启动后节点将会重新启用。

示例：在以下案例中，由于有 2 个节点关闭并且违反了 MaxPercentageUnhealthNodes 策略，因此群集暂时进入错误状态。 这是一个暂时性的错误，在修补操作继续后即可修复。

![不正常群集的示意图](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

如果问题持续出现，请参阅“故障排除”部分

问： **是否可以针对独立群集使用修补业务流程应用程序？**

A. 目前不可以，对独立群集的支持即将推出。

问： **修补业务流程应用程序处于警告状态**

A. 检查针对应用程序发布的运行状况报告是否是根本原因。 通常，警告中会包含问题的详细信息。
如果该问题是暂时性的，则应用程序应会自动从此状态恢复。

问： **群集运行不正常。但是，需要紧急执行 OS 更新**

A. 群集运行不正常时，修补业务流程应用程序不会安装更新。
请尝试将群集恢复正常状态，消除修补业务流程应用程序工作流的阻碍。

问： **为何运行群集修补需要花费很长时间？**

A. 修补业务流程应用程序花费的时间主要取决于以下因素：

- CoordinatorService 的策略 - `NodeWise` 的默认策略导致每次只修补一个节点。尤其是对于较大的群集，我们建议使用 `UpgradeDomainWise` 策略来更快地修补群集。
- 可下载并安装的更新数 - 下载并安装 某个更新所花费的时间不应超过数小时。
- VM 的性能和网络带宽。

## <a name="disclaimers"></a>免责声明

- 修补业务流程应用程序会代表用户接受 Windows 更新的 EULA。 可以在应用程序的配置中选择性地关闭该设置。

- 修补业务流程应用程序会收集遥测数据来跟踪使用情况和性能。
    应用程序遥测遵循 Service Fabric 运行时的遥测设置（默认设置）。

## <a name="troubleshooting-help"></a>故障排除步骤

### <a name="node-not-coming-back-to-up-state"></a>节点无法恢复启动状态

**节点可能持续处于“正在禁用”状态**如果由于挂起的安全检查而无法禁用针对操作计划的节点，则可能会发生此情况。 若要纠正此情况，请确保有足够多的节点处于正常状态。

**节点可能会出于以下原因已持续处于“已禁用”状态**

- 已手动禁用节点。
- 某个正在进行的 Azure 基础结构作业导致禁用了节点。
- 修补节点的修补业务流程应用程序暂时禁用了节点。

**节点可能会出于以下原因已持续处于“关闭”状态**

- 已手动将节点置于关闭状态。
- 节点正在重新启动（可能由修补业务流程应用程序触发）。
- VM/计算机故障或网络连接问题导致节点关闭。

### <a name="updates-were-skipped-on-some-nodes"></a>在某些节点上跳过了更新

修补业务流程应用程序会尝试根据重新计划策略安装 Windows 更新。 服务会根据应用程序策略尝试恢复节点并跳过更新。

在这种情况下，将会针对节点代理服务生成警告级别的运行状况报告。
Windows 更新结果也会包含可能的失败原因。

### <a name="health-of-the-cluster-goes-to-error-while-update-was-getting-installed"></a>安装更新时群集运行状况为出错

如果不正常的 Windows 更新使特定节点或升级域上的应用程序/群集的运行状况变差，则在群集恢复正常之前，修补业务流程应用程序不会继续进行任何后续的 Windows 更新操作。

管理员必须介入并了解 Windows 更新为何导致应用程序/群集的运行状况变差。

