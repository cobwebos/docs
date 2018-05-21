---
title: 适用于 Linux 的 Azure Service Fabric 修补业务流程应用程序 | Microsoft Docs
description: 用于在 Linux Service Fabric 群集中自动修补操作系统的应用程序。
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/22/2018
ms.author: nachandr
ms.openlocfilehash: f5d9b39a91567dd04b4e8ca0cd580c58024bb2f2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="patch-the-linux-operating-system-in-your-service-fabric-cluster"></a>在 Service Fabric 群集中修补 Linux 操作系统

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

修补业务流程应用程序是一个 Azure Service Fabric 应用程序，可在 Service Fabric 群集中自动修补操作系统，而无需停机。

修补业务流程应用提供以下功能：

- 自动的操作系统更新安装。 自动下载并安装操作系统更新。 可根据需要重新启动群集节点，且无需让群集停机。

- 群集感知修补和运行状况集成。 在应用更新时，修补业务流程应用会监视群集节点的运行状况。 群集节点的升级方式为一次一个节点，或一次一个升级域。 如果群集的运行状况由于修补进程而恶化，此时修补将停止以防止问题加重。

## <a name="internal-details-of-the-app"></a>应用的内部详细信息

修补业务流程应用由以下子组件组成：

- 协调器服务：此有状态服务负责：
    - 协调整个群集上的 OS 更新作业。
    - 存储已完成的 OS 更新操作的结果。
- 节点代理服务：此无状态服务在所有 Service Fabric 群集节点上运行。 该服务负责：
    - 在 Linux 上启动节点代理守护程序。
    - 监视守护程序服务。
- **节点代理守护程序**：此 Linux 守护程序服务以更高级别的特权 (root) 运行。 相比之下，节点代理服务和协调器服务以较低级别的特权运行。 该服务负责在所有群集节点上执行以下更新作业：
    - 在节点上禁用自动 OS 更新。
    - 根据用户提供的策略下载并安装 OS 更新。
    - 安装 OS 更新后，根据需要重启计算机。
    - 将 OS 更新的结果上传到协调器服务。
    - 在某个操作用完所有重试次数后失败时报告运行状况。

> [!NOTE]
> 修补业务流程应用使用 Service Fabric“修复管理器系统服务”来禁用/启用节点和执行运行状况检查。 修补业务流程应用创建的修复任务跟踪每个节点的更新进度。

## <a name="prerequisites"></a>先决条件

### <a name="ensure-that-your-azure-vms-are-running-ubuntu-1604"></a>确保 Azure VM 正在运行 Ubuntu 16.04
截至编写本文档时，Ubuntu 16.04 (`Xenial Xerus`) 是唯一受支持的版本。

### <a name="ensure-that-the-service-fabric-linux-cluster-is-version-61x-and-above"></a>确保 Service Fabric Linux 群集版本为 6.1.x 或以上

Linux 版修补业务流程应用使用特定的运行时功能，这些功能只能在 Service Fabric 运行时 6.1.x 或更高版本中使用。

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>启用修复管理器服务（如果尚未运行）

修补业务流程应用需要在群集上启用修复管理器系统服务。

#### <a name="azure-clusters"></a>Azure 群集

银级和金级持久层中的 Azure Linux 群集默认已启用修复管理器服务。 铜级持久层中的 Azure 群集默认不启用修复管理器服务。 如果已启用该服务，可以看到它在 Service Fabric Explorer 的系统服务部分中运行。

##### <a name="azure-portal"></a>Azure 门户
在设置群集时，可以从 Azure 门户启用修复管理器。 在配置群集时选择“附加功能”下的“包含修复管理器”选项。
![从 Azure 门户启用修复管理器的映像](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Azure 资源管理器部署模型
另外，也可以使用 [Azure 资源管理器部署模型](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)在新的或现有 Service Fabric 群集上启用修复管理器服务。 获取要部署的群集的模板。 可以使用示例模板，或者创建自定义 Azure 资源管理器部署模型模板。 

若要使用 [Azure 资源管理器部署模型模板](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)启用修复管理器服务，请执行以下操作：

1. 首先检查 `Microsoft.ServiceFabric/clusters` 资源的 `apiversion` 是否设置为 `2017-07-01-preview`。 如果不是，则需要将 `apiVersion` 更新为值 `2017-07-01-preview` 或更高的值：

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 现在，通过在 `fabricSettings` 节后面添加以下 `addonFeatures` 节来启用修复管理器服务：

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. 通过这些更改更新群集模板后，应用更改并等待升级完成。 现在可以看到修复管理器系统服务在群集中运行。 它在 Service Fabric Explorer 中的系统服务部分中被称为 `fabric:/System/RepairManagerService`。 

### <a name="standalone-on-premises-clusters"></a>独立本地群集

截至编写本文档时，Service Fabric Linux 独立群集不受支持。

### <a name="disable-automatic-os-update-on-all-nodes"></a>在所有节点上禁用自动 OS 更新

自动 OS 更新可能导致失去可用性，或者运行中应用程序的行为发生变化。 修补业务流程应用默认会尝试在每个群集节点上禁用自动 OS 更新，以防止出现此类情况。
对于 Ubuntu，修补业务流程应用会禁用 [unattended-upgrades](https://help.ubuntu.com/community/AutomaticSecurityUpdates)。

## <a name="download-the-app-package"></a>下载应用包

从该[下载链接](https://go.microsoft.com/fwlink/?linkid=867984)下载应用程序。

## <a name="configure-the-app"></a>配置应用

可配置修补业务流程应用的行为来满足需求。 在创建或更新应用程序的过程中，通过传入应用程序参数来替代默认值。 可以通过在 cmdlet `Start-ServiceFabricApplicationUpgrade` 或 `New-ServiceFabricApplication` 中指定 `ApplicationParameter` 来提供应用程序参数。

|**Parameter**        |**类型**                          | **详细信息**|
|:-|-|-|
|MaxResultsToCache    |Long                              | 应缓存的更新结果数上限。 <br>在假定以下情况时，默认值为 3000： <br> - 节点数为 20。 <br> - 节点上每月发生的更新次数为 5。 <br> - 每个操作的结果数可为 10。 <br> - 过去三个月的结果应已存储。 |
|TaskApprovalPolicy   |枚举 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy 指示协调器服务用于跨 Service Fabric 群集节点安装更新的策略。<br>                         允许值包括： <br>                                                           <b>NodeWise</b>。 每次在一个节点上安装更新。 <br>                                                           <b>UpgradeDomainWise</b>。 每次在一个升级域上安装更新。 （在最大程度情况下，属于升级域的所有节点都可进行更新。）
| UpdateOperationTimeOutInMinutes | int <br>（默认值：180）                   | 指示任何更新操作（下载或安装）的超时。 在指定的超时内未完成的操作会被中止。       |
| RescheduleCount      | int <br> （默认值：5）                  | 在操作持续失败的情况下，服务重新计划 OS 更新的最大次数。          |
| RescheduleTimeInMinutes  | int <br>（默认值：30） | 在持续失败的情况下，服务重新计划 OS 更新的间隔。 |
| UpdateFrequency           | 逗号分隔的字符串（默认值："Weekly, Wednesday, 7:00:00"）     | 在群集上安装 OS 更新的频率。 其格式和可能的值包括： <br>-   Monthly, DD, HH:MM:SS，例如：Monthly, 5,12:22:32。 <br> -   Weekly, DAY, HH:MM:SS，例如：Weekly, Tuesday, 12:22:32。  <br> -   Daily, HH:MM:SS，例如：Daily, 12:22:32。  <br> - None 表示不应执行更新。  <br><br> 所有时间采用 UTC 格式。|
| UpdateClassification | 逗号分隔的字符串（默认值：“securityupdates”） | 应在群集节点上安装的更新类型。 可接受的值为 securityupdates、all。 <br> -  securityupdates - 只安装安全更新 <br> -  all - 安装 apt 中的所有可用更新。|
| ApprovedPatches | 逗号分隔的字符串（默认值：""） | 这是应在群集节点上安装的已批准更新列表。 逗号分隔的列表包含已批准的包和可选的所需目标版本。<br> 例如："apt-utils = 1.2.10ubuntu1, python3-jwt, apt-transport-https < 1.2.194, libsystemd0 >= 229-4ubuntu16" <br> 上述代码会安装 <br> - 包含版本 1.2.10ubuntu1 的 apt-utils（如果 apt-cache 中已提供）。 如果未提供该特定版本，则不执行任何操作。 <br> - python3-jwt 升级到最新可用版本。 如果该包不存在，则不执行任何操作。 <br> - apt-transport-https 升级到低于 1.2.194 的最高版本。 如果该版本不存在，则不执行任何操作。 <br> - libsystemd0 升级到大于等于 229-4ubuntu16 的最高版本。 如果此类版本不存在，则不执行任何操作。|
| RejectedPatches | 逗号分隔的字符串（默认值：""） | 这是不应在群集节点上安装的更新列表。 <br> 例如："bash, sudo" <br> 上述代码会从更新接收项目中筛选出 bash、sudo。 |


> [!TIP]
> 若要立即进行 OS 更新，请依据应用程序部署时间设置 `UpdateFrequency`。 例如，假设拥有一个 5 节点测试群集，并计划在大约 UTC 下午 5:00 部署应用。 如果假定应用程序升级或部署最多需要 30 分钟，请将 UpdateFrequency 设置为“Daily, 17:30:00”。

## <a name="deploy-the-app"></a>部署应用

1. 完成所有先决条件步骤来准备群集。
2. 像部署任何其他 Service Fabric 应用那样部署修补业务流程应用。 可以使用 PowerShell 或 Azure Service Fabric CLI 部署应用。 遵循[使用 PowerShell 部署和删除应用程序](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)或[使用 Azure Service Fabric CLI 部署应用程序](https://docs.microsoft.com/azure/service-fabric/scripts/cli-deploy-application)中的步骤。
3. 若要在部署时配置应用程序，请将 `ApplicationParamater` 传递给 `New-ServiceFabricApplication` cmdlet 或提供的脚本。 为方便起见，我们随应用程序一同提供了 powershell (Deploy.ps1) 和 bash (Deploy.sh) 脚本。 使用脚本：

    - 连接到 Service Fabric 群集。
    - 执行部署脚本。 （可选）将应用程序参数传递给脚本。 例如：.\Deploy.ps1 -ApplicationParameter @{ UpdateFrequency = "Daily, 11:00:00"} OR ./Deploy.sh "{\"UpdateFrequency\":\"Daily, 11:00:00\"}" 

> [!NOTE]
> 保持脚本和应用程序文件夹 PatchOrchestrationApplication 位于同一目录中。

## <a name="upgrade-the-app"></a>升级应用

若要升级现有的修补业务流程应用，请遵循[使用 PowerShell 进行 Service Fabric 应用程序升级](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)或[使用 Azure Service Fabric CLI 进行 Service Fabric 应用程序升级](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade)中的步骤。

## <a name="remove-the-app"></a>删除应用

若要删除应用程序，请遵循[使用 PowerShell 部署和删除应用程序](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)或[使用 Azure Service Fabric CLI 删除应用程序](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-delete)中的步骤。

为方便起见，我们随应用程序一同提供了 powershell (Undeploy.ps1) 和 bash (Undeploy.sh) 脚本。 使用脚本：

  - 连接到 Service Fabric 群集。
  - 执行脚本 Undeploy.ps1 或 Undeploy.sh

> [!NOTE]
> 保持脚本和应用程序文件夹 PatchOrchestrationApplication 位于同一目录中。

## <a name="view-the-update-results"></a>查看更新结果

修补业务流程应用公开了 REST API，以向用户显示历史结果。 下面是一个示例结果：```testadm@bronze000001:~$ curl -X GET http://10.0.0.5:20002/PatchOrchestrationApplication/v1/GetResults```
```json
[ 
  { 
    "NodeName": "_bronze_0", 
    "UpdateOperationResults": [ 
      { 
        "OperationResult": "succeeded", 
        "NodeName": "_bronze_0", 
        "OperationTime": "2017-11-21T12:39:29.0435917Z", 
        "UpdateDetails": [ 
          { 
            "UpdateId": "linux-cloud-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-headers-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-image-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "python3-apport:amd64=2.20.1-0ubuntu2.13", 
            "ResultCode": "succeeded" 
          }, 
        ], 
        "OperationType": "installation", 
        "UpdateClassification": "securityupdates", 
        "UpdateFrequency": "Daily, 7:00:00", 
        "RebootRequired": true, 
        "ApprovedList": "", 
        "RejectedList": "" 
      } 
    ] 
  } 
] 
```

下面描述了 JSON 的字段：

字段 | 值 | 详细信息
-- | -- | --
OperationResult | 0 - Succeeded（成功）<br> 1 - Succeeded With Errors（已成功但存在错误）<br> 2 - Failed（失败）<br> 3 - Aborted（已中止）<br> 4 - Aborted With Timeout（因超时已中止） | 指示整个操作（通常涉及安装一个或多个更新）的结果。
ResultCode | 与 OperationResult 相同 | 此字段指示单个更新的安装操作的结果。
OperationType | 1 - Installation（安装）<br> 0 - Search and Download（搜索并下载）。| Installation 是默认情况下结果中将显示的唯一 OperationType。
UpdateClassification | 默认值为“securityupdates” | 在执行更新操作期间安装的更新类型
UpdateFrequency | 默认值为“Weekly, Wednesday, 7:00:00” | 为此更新配置的更新频率。
RebootRequired | true - 需要重新启动<br> false - 无需重新启动 | 指示是否需要重新启动才能完成安装更新。
ApprovedList | 默认值为 "" | 此更新批准的修补程序列表
RejectedList | 默认值为 "" | 此更新拒绝的修补程序列表

如果尚未计划更新，JSON 结果将为空。

登录到群集以查询更新结果。 然后找出协调器服务的主副本地址，并在浏览器中点击此 URL：http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetResults。

协调器服务的 REST 终结点有一个动态端口。 若要检查确切的 URL，请参考 Service Fabric Explorer。 例如，可在 `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetResults` 处获取结果。

![REST 终结点的图像](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

## <a name="diagnosticshealth-events"></a>诊断/运行状况事件

### <a name="diagnostic-logs"></a>诊断日志

修补业务流程应用日志是作为 Service Fabric 运行日志的一部分进行收集的。

在想要通过所选的诊断工具/管道捕获日志的情况下使用。 修补业务流程应用程序使用以下固定的提供程序 ID 通过 [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netstandard-2.0) 记录事件

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>运行状况报告

对于以下情况，修补业务流程应用还会针对协调器服务或节点代理服务发布运行状况报告：

#### <a name="an-update-operation-failed"></a>更新操作失败

如果某个节点上的更新操作失败，将会针对节点代理服务生成运行状况报告。 运行状况报告的详细信息将包含有问题的节点名称。

在有问题的节点上成功完成修补后，会自动清除该报告。

#### <a name="the-node-agent-daemon-service-is-down"></a>节点代理守护程序服务已关闭

如果某个节点上的节点代理守护程序服务关闭，将会针对节点代理服务生成警告级别的运行状况报告。

#### <a name="the-repair-manager-service-is-not-enabled"></a>未启用修复管理器服务

如果在群集上找不到修复管理器服务，将会针对协调器服务生成警告级别的运行状况报告。

## <a name="frequently-asked-questions"></a>常见问题

问： 为什么在修补业务流程应用运行时，我发现群集处于错误状态？

A. 在安装过程中，修补业务流程应用会禁用或重启节点。 此操作可能暂时导致群集的运行状况变差。

根据应用程序的策略，执行修补操作期间可以有一个节点关闭，或者整个升级域同时关闭。

在安装结束时，重启后节点将会重新启用。

在以下示例中，由于两个节点关闭且违反了 MaxPercentageUnhealthyNodes 策略，群集暂时进入了错误状态。 这是一个暂时性的错误，在修补操作继续后即可恢复。

![不正常群集的图像](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

如果问题持续出现，请参阅“故障排除”部分。

问： 修补业务流程应用处于警告状态

A. 检查针对应用程序发布的运行状况报告是否是根本原因。 通常，警告中会包含问题的详细信息。 如果该问题是暂时性的，则应用程序应该会自动从此状态中恢复。

问： 如果群集运行不正常，而我需要进行紧急的操作系统更新，该怎么办？

A. 群集运行不正常时，修补业务流程应用不会安装更新。 若要消除修补业务流程应用工作流的阻碍，请将群集恢复正常状态。

问： 为何跨群集运行修补需要花费很长时间？

A. 修补业务流程应用所需的时长主要取决于以下因素：

- 协调器服务的策略。 
  - 默认策略 `NodeWise` 指定一次只修补一个节点。 尤其是当存在更大的群集时，我们建议使用 `UpgradeDomainWise` 策略以实现更快的跨群集修补。
- 可下载并安装的更新数。 
- 下载和安装更新所需的平均时间，不应超过两个小时。
- VM 的性能和网络带宽。

问： **修补业务流程应用如何判断哪些更新是安全更新？**

A. 修补业务流程应用使用特定于分发版的逻辑来确定可用更新中的哪些更新是安全更新。 例如，在 ubuntu 中，应用会搜索存档 $RELEASE-security、$RELEASE-updates 中的更新（$RELEASE 为 Xenial 或 Linux 标准基础发行版）。 

 
问： **如何锁定为特定的包版本？**

A. 使用 ApprovedPatches 设置可将包锁定为特定版本。 


问： **Ubuntu 中启用的自动更新如何进行？**

A. 在群集上安装修补业务流程应用后，会立即禁用群集节点上的无人参与升级。 所有定期更新工作流将由修补业务流程应用驱动。
若要在整个群集中实现环境一致性，我们建议只通过修补业务流程应用安装更新。 
 
问： **在升级后，修补业务流程应用是否会清理未使用的包？**

A. 是的，在执行安装后的步骤期间会执行清理。 

## <a name="troubleshooting"></a>故障排除

### <a name="a-node-is-not-coming-back-to-up-state"></a>节点无法恢复启动状态

节点可能会卡在“正在禁用”状态，因为：

安全检查处于挂起中。 若要纠正此情况，请确保有足够多的节点处于正常状态。

节点可能会卡在“已禁用”状态，因为：

- 节点已被手动禁用。
- 某个正在进行的 Azure 基础结构作业导致节点被禁用。
- 修补节点的修补业务流程应用暂时禁用了节点。

节点可能会卡在关闭状态，因为：

- 已手动将节点置于关闭状态。
- 节点正在重新启动（可能由修补业务流程应用触发）。
- VM 或计算机故障、网络连接问题导致节点关闭。

### <a name="updates-were-skipped-on-some-nodes"></a>在某些节点上跳过了更新

修补业务流程应用根据重新计划策略尝试安装更新。 服务根据应用程序策略尝试恢复节点并跳过更新。

在这种情况下，将针对节点代理服务生成警告级别的运行状况报告。 更新结果也包含可能的失败原因。

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>安装更新时群集运行状况出错

发生故障的更新会使特定节点或升级域上的应用程序或群集的运行状况恶化。 修补业务流程应用会终止任何后续的更新操作，直到群集再次正常运行。

管理员必须介入，并判断为何之前安装的更新会导致应用程序或群集不正常。

## <a name="disclaimer"></a>免责声明

修补业务流程应用会收集遥测来跟踪使用情况和性能。 应用程序遥测遵循 Service Fabric 运行时的遥测设置（默认设置）。

## <a name="release-notes"></a>发行说明

### <a name="version-010"></a>版本 0.1.0
- 个人预览版

### <a name="version-200-latest"></a>版本 2.0.0（最新版本）
- 公开发布的版本
