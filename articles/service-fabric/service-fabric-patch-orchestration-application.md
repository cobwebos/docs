---
title: 修补 Service Fabric 群集中的 Windows 操作系统 |Microsoft Docs
description: 本文介绍如何使用修补业务流程应用程序在 Service Fabric 群集上自动进行操作系统修补。
services: service-fabric
documentationcenter: .net
author: khandelwalbrijeshiitr
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: brkhande
ms.openlocfilehash: a02228593a9d8efc9fb363232da1cede3c80a8b3
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592529"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>在 Service Fabric 群集中修补 Windows 操作系统

> 
> [!IMPORTANT]
> 从2019年4月30日起，不再支持修补业务流程应用程序版本 1.2. *。 请确保升级到最新版本。

> [!NOTE]
> 若要在[虚拟机规模集上进行自动 OS 映像升级](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)，最佳做法是保持操作系统在 Azure 中进行修补。 基于虚拟机规模集的自动 OS 映像升级需要在规模集上具有白银或更高的持久性。
>

 修补业务流程应用程序（POA）是一个围绕 Azure Service Fabric 修复管理器服务的包装，为非 Azure 托管群集启用基于配置的操作系统修补程序计划。 非 Azure 托管群集不需要 POA，但必须通过更新域计划修补程序安装，才能修补 Service Fabric 群集主机，而不会导致停机。

POA 是一种 Service Fabric 的应用程序，它在 Service Fabric 群集上自动进行操作系统修补，而不会导致停机。

POA 提供以下功能：

- 自动的操作系统更新安装。 自动下载并安装操作系统更新。 群集节点会根据需要重新启动，而不会导致群集停机。

- 群集感知修补和运行状况集成。 当 POA 应用更新时，它会监视群集节点的运行状况。 群集节点一次更新一个节点，或一次更新一个更新域。 如果群集的运行状况由于修补进程而停机，则会停止修补，以防加重问题。

## <a name="internal-details-of-poa"></a>POA 的内部详细信息

POA 由以下子组件组成：

- 协调器服务：此有状态服务负责：
    - 协调整个群集上的 Windows 更新作业。
    - 存储已完成的 Windows 更新操作的结果。

- 节点代理服务：此无状态服务在所有 Service Fabric 群集节点上运行。 该服务负责：
    - 启动节点代理 NTService。
    - 监视节点代理 NTService。

- 节点代理 NTService：此 Windows NT 服务以更高级别的特权 (SYSTEM) 运行。 相比之下，节点代理服务和协调器服务以较低级别的特权 (NETWORK SERVICE) 运行。 该服务负责在所有群集节点上执行以下 Windows 更新作业：
    - 在节点上禁用自动 Windows 更新。
    - 根据用户提供的策略下载并安装 Windows 更新。
    - 正在重新启动计算机 Windows 更新后安装。
    - 将 Windows 更新的结果上传到协调器服务。
    - 如果操作在用完所有重试后失败，则报告运行状况报告。

> [!NOTE]
> POA 使用 Service Fabric 修复管理器服务来禁用或启用节点并执行运行状况检查。 POA 创建的修复任务跟踪每个节点的 Windows 更新进度。

## <a name="prerequisites"></a>必备组件

> [!NOTE]
> 所需的最低 .NET Framework 版本为4.6。

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>启用修复管理器服务（如果尚未运行）

POA 要求在群集上启用修复管理器服务。

#### <a name="azure-clusters"></a>Azure 群集

默认情况下，银持久性层中的 Azure 群集默认启用了修复管理器服务。 黄金持久性层中的 Azure 群集可能会也可能不会启用修复管理器服务，具体取决于这些群集的创建时间。 默认情况下，铜牌持久性层中的 Azure 群集未启用修复管理器服务。 如果已启用该服务，则可以在 Service Fabric Explorer 的 "系统服务" 部分中看到该服务正在运行。

##### <a name="the-azure-portal"></a>Azure 门户
设置群集时，可以启用 Azure 门户中的修复管理器。 配置群集时，请选择 "**附加功能**" 下的 "**包括修复管理器**" 选项。

![从 Azure 门户启用修复管理器的图像](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Azure 资源管理器部署模型
或者，你可以使用[Azure 资源管理器部署模型](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)在新的和现有 Service Fabric 群集上启用修复管理器服务。 获取要部署的群集的模板。 可以使用示例模板，或者创建自定义 Azure 资源管理器部署模型模板。 

若要使用[Azure 资源管理器部署模型模板](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)启用修复管理器服务，请执行以下操作：

1. 检查以确保*ServiceFabric/群集*资源 `apiVersion` 设置为*2017-07-01-preview* 。 如果不同，则需要将 `apiVersion` 更新到*2017-07-01-preview*或更高版本：

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. 通过在 `fabricSettings` 部分后面添加以下 `addonFeatures` 部分来启用修复管理器服务：

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. 使用这些更改更新群集模板后，应用这些更改并让更新完成。 现在，你可以看到在群集中运行的修复管理器服务。 它在 Service Fabric Explorer 的 "系统服务" 部分中称为*fabric：/system/RepairManagerService* 。 

### <a name="standalone-on-premises-clusters"></a>独立本地群集

若要在新的或现有的 Service Fabric 群集上启用修复管理器服务，可以使用[独立 Windows 群集的配置设置](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)。

若要启用修复管理器服务：

1. 检查以确保[常规群集配置](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations)中的 `apiVersion` 设置为*04-2017*或更高版本，如下所示：

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. 通过在 `fabricSettings` 部分后面添加以下 `addonFeatures` 部分来启用修复管理器服务，如下所示：

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. 使用已更新的群集清单[创建新群集](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server)或[升级群集配置](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server)，使用这些更改更新群集清单。 

   使用更新的群集清单运行群集后，可以看到在群集中运行的修复管理器服务。 它称为*fabric：/system/RepairManagerService*，它位于 Service Fabric Explorer 的 "系统服务" 部分。

### <a name="configure-windows-updates-for-all-nodes"></a>为所有节点配置 Windows 更新

自动 Windows 更新可能导致可用性丢失，因为多个群集节点可以同时重新启动。 默认情况下，POA 会尝试在每个群集节点上禁用自动 Windows 更新。 但是，如果设置由管理员或组策略进行管理，我们建议将 Windows 更新策略设置为 "下载之前通知"。

## <a name="download-the-application-package"></a>下载应用程序包

若要下载应用程序包，请参阅 GitHub 上的[修补业务流程应用程序版本页](https://github.com/microsoft/Service-Fabric-POA/releases/latest/)。

## <a name="configure-poa-behavior"></a>配置 POA 行为

你可以配置 POA 行为以满足你的需求。 在创建或更新应用程序时，通过传入应用程序参数来替代默认值。 可以通过将 `ApplicationParameter` 指定到 `Start-ServiceFabricApplicationUpgrade` 或 `New-ServiceFabricApplication` cmdlet 来提供应用程序参数。

| 参数        | Type                          | 详细信息 |
|:-|-|-|
|MaxResultsToCache    |Long                              | 应缓存的 Windows 更新结果的最大数量。 <br><br>默认值为3000，假设： <br> &nbsp; &nbsp;-节点数为20。 <br> &nbsp; &nbsp;-每个节点的更新次数为5。 <br> &nbsp; &nbsp;-每个操作的结果数可为10。 <br> &nbsp; &nbsp;-应存储过去三个月的结果。 |
|TaskApprovalPolicy   |枚举 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy 指示协调器服务用于跨 Service Fabric 群集节点安装 Windows 更新的策略。<br><br>允许的值为： <br>*NodeWise*：每次一个节点安装 Windows 更新。 <br> *UpgradeDomainWise*：每次安装一个更新域的 Windows 更新。 （在大多数情况下，属于一个更新域的所有节点都可以进行 Windows 更新。）<br><br> 为了帮助确定哪种策略最适合你的群集，请参阅[常见问题解答](#frequently-asked-questions)部分。
|LogsDiskQuotaInMB   |Long  <br> （默认值： *1024*）               | 修补业务流程应用日志的最大大小（MB），可以在节点上本地保存。
| WUQuery               | 字符串<br>（默认值： *IsInstalled = 0*）                | 用于获取 Windows 更新的查询。 有关详细信息，请参阅 [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)。
| InstallWindowsOSOnlyUpdates | *布尔值* <br> （默认值：false）                 | 使用此标志来控制应当下载并安装哪些更新。 允许以下值 <br>true - 仅安装 Windows 操作系统更新。<br>false - 在计算机上安装所有可用的更新。          |
| WUOperationTimeOutInMinutes | Int <br>（默认值： *90*）                   | 指示任何 Windows 更新操作（搜索、下载或安装）的超时。 在指定的超时内未完成的操作会被中止。       |
| WURescheduleCount     | Int <br> （默认值： *5*）                  | 如果操作永久失败，服务会重新安排 Windows 更新的最大次数。          |
| WURescheduleTimeInMinutes | Int <br>（默认值： *30*） | 如果故障仍然存在，服务重新安排 Windows 更新的时间间隔。 |
| WUFrequency           | 逗号分隔的字符串（默认值：*每周、星期三、7:00:00*）     | 安装 Windows 更新的频率。 其格式和可能的值包括： <br>&nbsp; &nbsp;-每月： DD，HH： MM： SS （例如，*月度，5，12：22： 32*）<br>字段 DD （day）的允许值是从1到28和 "last" 的数字。 <br> &nbsp; &nbsp;-周历，DAY，HH： MM： SS （例如*每周、星期二、12:22:32*）  <br> &nbsp; &nbsp; 日，HH： MM： SS （例如，*每天，12:22:32*）  <br> &nbsp; &nbsp; -   "*无*" 表示不应执行 Windows 更新。  <br><br> 时间采用 UTC 格式。|
| AcceptWindowsUpdateEula | 布尔 <br>（默认值： *true*） | 通过设置此标志，该应用程序将代表计算机所有者接受 Windows 更新的最终用户许可协议。              |

> [!TIP]
> 如果希望立即进行 Windows 更新，请将 `WUFrequency` 设置为相对于应用程序部署时间。 例如，假设拥有一个 5 节点测试群集，并计划在大约 UTC 下午 5:00 部署应用。 如果你认为应用程序升级或部署最多需要30分钟，请将 Wufrequency 设置设置为 "*每天" 17:30:00*。

## <a name="deploy-poa"></a>部署 POA

1. 完成所有先决条件步骤来准备群集。
1. 像部署任何其他 Service Fabric 应用一样部署 POA。 若要使用 PowerShell 进行部署，请参阅[使用 Powershell 部署和删除应用程序](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)。
1. 若要在部署时配置应用程序，将 `ApplicationParameter` 传递至 `New-ServiceFabricApplication` cmdlet。 为方便起见，我们随应用程序一同提供了脚本 Deploy.ps1。 使用脚本：

    - 使用 `Connect-ServiceFabricCluster` 连接到 Service Fabric 群集。
    - 结合相应的 `ApplicationParameter` 值执行 PowerShell 脚本 Deploy.ps1。

> [!NOTE]
> 将脚本和应用程序文件夹*PatchOrchestrationApplication*在同一目录中。

## <a name="upgrade-poa"></a>升级 POA

若要使用 PowerShell 升级 POA 版本，请按照[使用 powershell Service Fabric 应用程序升级](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)中的说明进行操作。

## <a name="remove-poa"></a>删除 POA

若要删除应用程序，请按照[使用 PowerShell 部署和删除应用程序](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)中的说明进行操作。

为方便起见，我们提供了与应用程序一起提供的取消部署的 ps1 脚本。 使用脚本：

  - 使用 ```Connect-ServiceFabricCluster``` 连接到 Service Fabric 群集。
  - 执行 PowerShell 脚本 Undeploy.ps1。

> [!NOTE]
> 将脚本和应用程序文件夹*PatchOrchestrationApplication*在同一目录中。

## <a name="view-the-windows-update-results"></a>查看 Windows 更新结果

POA 公开 REST Api 以向用户显示历史结果。 下面是结果 JSON 的示例：

```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

下表描述了 JSON 字段：

字段 | 值 | 详细信息
-- | -- | --
OperationResult | 0 - Succeeded（成功）<br> 1 - Succeeded With Errors（已成功但存在错误）<br> 2 - Failed（失败）<br> 3 - Aborted（已中止）<br> 4 - Aborted With Timeout（因超时已中止） | 指示整体操作的结果，通常包括一个或多个更新的安装。
ResultCode | 与 OperationResult 相同 | 此字段指示单个更新的安装操作的结果。
OperationType | 1 - Installation（安装）<br> 0-搜索和下载| 默认情况下，安装是结果中显示的唯一 OperationType。
WindowsUpdateQuery | 默认值是“IsInstalled=0” | 用于搜索更新的 Windows 更新查询。 有关详细信息，请参阅[WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)。
RebootRequired | true - 需要重新启动<br> false - 无需重新启动 | 指示是否需要重新启动才能完成更新的安装。
OperationStartTime | 日期/时间 | 指示操作（下载/安装）开始的时间。
OperationTime | 日期/时间 | 指示操作（下载/安装）完成的时间。
HResult | 0-成功<br> 其他-失败| 指示 updateID "7392acaf-6a85-427c-8a8d-058c25beb0d6" 的 Windows 更新失败的原因。

如果尚未计划更新，JSON 结果将为空。

登录到群集以查询 Windows 更新结果。 找出协调器服务的主要地址的副本 IP 地址，并从浏览器中打开以下 URL： http://&lt;REPLICA-IP &gt;： &lt;ApplicationPort &gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

协调器服务的 REST 终结点有一个动态端口。 若要检查确切的 URL，请参阅 Service Fabric Explorer。 例如， *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults* 上提供结果。

![REST 终结点的图像](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

如果在群集上启用了反向代理，则也可以从群集外部访问该 URL。

需要命中的终结点是*http://&lt;SERVERURL &gt;： &lt;REVERSEPROXYPORT &gt;/patchorchestrationapplication/coordinatorservice/v1/getwindowsupdateresults*。

若要在群集上启用反向代理，请按照[Azure Service Fabric 中的反向代理](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)中的说明进行操作。 

> 
> [!WARNING]
> 配置反向代理后，可从群集外部寻址群集中公开 HTTP 终结点的所有微服务。

## <a name="diagnostics-and-health-events"></a>诊断和运行状况事件

本部分介绍如何通过 POA 上的在 Service Fabric 群集上调试或诊断修补程序的问题。

> [!NOTE]
> 若要获取许多以下称为 "自诊断" 的增强功能，应安装 POA 版本1.4.0 或更高版本。

Node Agent NTService 创建用于在节点上安装更新的[修复任务](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet)。 然后，由协调器服务根据任务批准策略来准备每个任务。 最后，修复管理器批准了准备好的任务，如果群集处于不正常状态，则不会批准任何任务。 

为了帮助你了解如何在节点上进行更新，让我们逐步逐步执行以下操作：

1. NodeAgentNTService 在每个节点上运行，在计划时间查找可用的 Windows 更新。 如果有可用的更新，它将在节点上下载它们。

1. 下载更新后，节点代理 NTService 将为名称为*POS___ \<unique_id >* 的节点创建相应的修复任务。 你可以通过使用[ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) cmdlet 或在 "节点详细信息" 部分中使用 SFX 来查看这些修复任务。 创建修复任务后，它会迅速移动到[*声明*状态](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet)。

1. 协调器服务定期查找处于*声明*状态的修复任务，然后更新这些任务以根据 TaskApprovalPolicy*准备*状态。 如果将 TaskApprovalPolicy 配置为 NodeWise，则只有在当前没有其他修复任务*准备*、*批准*、*执行*或*还原*状态时，才会准备好与节点对应的修复任务。 

   同样，在 UpgradeWise TaskApprovalPolicy 的情况下，仅针对属于同一更新域的节点提供了上述状态的任务。 将修复任务移动到*准备*状态后，将[禁用](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps)相应的 Service Fabric 节点，目的设置为 "*重新启动*"。

   POA 版本1.4.0 和更高版本通过 CoordinatorService 上的 ClusterPatchingStatus 属性发布事件，以显示正在修补的节点。 更新安装在 _poanode_0 上，如下图所示：

    [群集修补状态 ![Image](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. 节点禁用后，修复任务将移动到*执行*状态。 
   
   > [!NOTE]
   > 停滞处于*禁用*状态的节点会阻止新的修复任务，这会停止群集上的修补操作。

1. 当修复任务处于*执行*状态时，该节点上的修补程序安装将开始。 安装修补程序后，根据修补程序，节点可能会或不会重新启动。 接下来，将修复任务移动到*还原*状态，该状态会节点。 然后将修复任务标记为已完成。

   在 POA 版本1.4.0 和更高版本中，可以通过使用 \<NodeName WUOperationStatus > 属性查看 NodeAgentService 上的运行状况事件来查找更新的状态。 下图中突出显示的部分显示了*poanode_0*和*poanode_2*节点上的 Windows 更新的状态：

   [Windows 更新操作状态的 ![Image](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [Windows 更新操作状态的 ![Image](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   你还可以使用 PowerShell 获取详细信息。 为此，请使用[ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps)连接到群集并提取修复任务的状态。 
   
   在下面的示例中，"POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" 任务处于*DownloadComplete*状态。 这意味着更新已下载到*poanode_2*节点上，并且在任务移动到*执行*状态时将尝试安装。

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   如果仍发现更多问题，请登录到虚拟机（VM）或 Vm，并使用 Windows 事件日志来了解相关信息。 前面提到的修复任务只能存在于以下执行程序 substates 中：

      ExecutorSubState | 描述
    -- | -- 
      无 = 1 |  表示节点上没有正在进行的操作。 状态可能处于转换状态。
      DownloadCompleted = 2 | 表示下载操作成功完成、部分失败或失败。
      InstallationApproved = 3 | 表示下载操作之前已完成，修复管理器已批准安装。
      InstallationInProgress = 4 | 对应于修复任务的执行状态。
      InstallationCompleted = 5 | 表示安装已成功、部分成功或失败。
      RestartRequested = 6 | 表示修补程序安装已完成，并且节点上有挂起的重新启动操作。
      RestartNotNeeded = 7 |  表示修补程序安装完成后不需要重新启动。
      RestartCompleted = 8 | 表示重启已成功完成。
      OperationCompleted = 9 | Windows 更新操作已成功完成。
      OperationAborted = 10 | 表示 Windows 更新操作被中止。

1. 在 POA 版本1.4.0 及更高版本中，当节点更新尝试完成时，将在 NodeAgentService 上发布包含 "WUOperationStatus-[NodeName]" 属性的事件，以便在下一次下载和安装 Windows 更新时通知您。 如下图所示：

     [Windows 更新操作状态的 ![Image](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>诊断日志

修补业务流程应用程序日志作为 Service Fabric 运行时日志的一部分收集。

你可以使用所选的诊断工具或管道捕获日志。 POA 使用以下固定的提供程序 Id 通过[事件源](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)记录事件：

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>运行状况报告

在以下情况下，POA 还会针对节点代理服务或协调器服务发布运行状况报告：

* 节点代理 NTService 关闭

   如果某个节点上的节点代理 NTService 关闭，将会针对节点代理服务生成警告级别的运行状况报告。

* 未启用修复管理器服务

   如果在群集上找不到修复管理器服务，则会为协调器服务生成警告级别的运行状况报告。

## <a name="frequently-asked-questions"></a>常见问题

**问：当 POA 正在运行时，为什么会看到群集处于错误状态？**

答：在安装过程中，POA 将禁用或重新启动节点，这可能会暂时导致群集不正常。

根据应用程序的策略，在修补操作期间一个节点可能会关闭，*或者*整个更新域会同时关闭。

在 Windows 更新安装结束时，节点将在重新启动后重新启用。

在以下示例中，由于两个节点关闭且违反了 MaxPercentageUnhealthyNodes 策略，群集暂时进入了错误状态。 此错误是暂时的，直到修补操作开始。

![不正常群集的图像](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

如果问题持续出现，请参阅“故障排除”部分。

**问：如果 POA 处于警告状态，我该怎么办？**

答：检查是否已针对应用程序发布的运行状况报告指示根本原因。 通常，警告中会包含问题的详细信息。 如果该问题是暂时性的，则应用程序应自动恢复。

**问：如果群集运行不正常，需要进行紧急的操作系统更新，该怎么办？**

答：当群集不正常时，POA 不会安装更新。 尝试使群集处于正常状态以解除阻止 POA 工作流。

**问：我是否应该为群集将 TaskApprovalPolicy 设置为 "NodeWise" 或 "UpgradeDomainWise"？**

答：通过以并行方式修补属于更新域的所有节点，"UpgradeDomainWise" 设置加速总体群集修复。 在此过程中，属于整个更新域的节点不可用（处于[*禁用*状态](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)）。

与此相反，"NodeWise" 设置一次只修补一个节点，这意味着总体群集修补可能需要更长时间。 但是，在修补过程中，最多只能有一个节点不可用（处于*禁用*状态）。

如果你的群集可以容忍在修补周期（其中 N 是你的群集上的更新域的总数）上在 N-1 个更新域上运行，则可以将该策略设置为 "UpgradeDomainWise"。 否则，请将其设置为 "NodeWise"。

**问：修补节点需要多长时间？**

答：修补节点可能需要几分钟（例如， [Windows Defender 定义更新](https://www.microsoft.com/en-us/wdsi/definitions)）到几小时（例如， [windows 累积更新](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)）。 修补节点所需的时间主要取决于： 
 - 更新的大小。
 - 更新数，必须在修补窗口中应用。
 - 安装更新所需的时间，重新启动节点（如果需要），并完成重新启动后安装步骤。
 - VM 或计算机的性能以及网络条件。

**问：修补整个群集需要多长时间？**

答：修补整个群集所需的时间取决于：

- 修补节点所需的时间。

- 协调器服务的策略。 默认策略 "NodeWise" 会导致一次只修补一个节点，这种方法比使用 "UpgradeDomainWise" 要慢。 

   例如：如果某个节点要修补大约1小时，则修补包含5个更新域（每个都包含4个节点）的20节点（相同类型的节点）群集需要：
    - 对于 "NodeWise"：约20小时。
    - 对于 "UpgradeDomainWise"：约5小时。

- 群集负载。 每次修补操作都需要将客户工作负荷重新定位到群集中的其他可用节点。 正在进行修补的节点会在此期间处于[*禁用*状态](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling)。 如果群集的运行接近峰值负载，禁用进程将花费更长时间。 因此，在这种情况下，整体修补过程的速度可能会很慢。

- 修补期间的群集运行状况错误。 [群集运行状况的](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction)任何[降级](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error)都将中断修补过程。 此问题将增加修补整个群集所需的总时间。

**问：为什么在 Windows 更新的结果中看到一些更新，这些更新是通过 REST API 获取的，而不是在计算机上的 Windows 更新历史记录下？**

答：某些产品更新仅在其自己的更新或修补程序历史记录中出现。 例如，Windows Defender 更新可能会显示在 Windows Server 2016 Windows 更新历史记录中，也可能不显示。

**问：是否可以使用 POA 修补我的开发群集（单节点群集）？**

答：不可以，POA 不能用于修补单节点群集。 此限制是设计使然，因为[Service Fabric 系统服务](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services)或其他客户应用会导致停机。 因此，修补修复作业的修复管理器不会获得批准。

**问：在 Linux 上如何实现修补群集节点？**

答：若要了解如何在 Linux 上协调更新，请参阅[Azure 虚拟机规模集自动 OS 映像升级](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)。

**问：为什么更新循环需要花费很长时间？**

答：查询结果 JSON，为所有节点输入更新循环，然后可以使用 OperationStartTime 和 OperationTime （OperationCompletionTime）尝试查找每个节点上安装更新所需的时间。 

如果有很大的时间范围内没有进行更新，则群集可能处于错误状态，因此修复管理器无法批准任何 POA 修复任务。 如果某个节点上的更新安装需要很长时间，则该节点可能在一段时间内未更新。 许多更新可能会等待安装，这可能会导致延迟。 

由于节点修补处于*禁用*状态，因此也可能会阻止节点修补。 发生这种情况的原因通常是禁用节点可能导致仲裁或数据丢失。

**问：为什么在 POA 修补节点时，该节点必须处于禁用状态？**

答： POA 使用*重启*意向禁用节点，该节点停止或重新分配节点上运行的所有 Service Fabric 服务。 POA 这样做是为了确保应用程序不会使用新的和旧的 Dll 组合结束，因此，我们建议不要在不禁用节点的情况下修补节点。

## <a name="disclaimers"></a>免责声明

- POA 代表用户接受 Windows 更新的最终用户许可协议。 可以在应用程序的配置中选择性地关闭该设置。

- POA 收集遥测数据，以跟踪使用情况和性能。 应用程序遥测遵循 Service Fabric 运行时的遥测设置（默认设置）。

## <a name="troubleshooting"></a>故障排除

本部分提供对修补节点问题进行故障排除的可能解决方案。

### <a name="a-node-is-not-coming-back-to-up-state"></a>节点无法恢复启动状态

* 节点可能会停滞处于*禁用*状态，原因如下：

  - 安全检查处于挂起中。 若要纠正此情况，请确保有足够多的节点处于正常状态。

* 节点可能会停滞处于*禁用*状态，原因如下：

  - 它已被手动禁用。
  - 由于正在进行的 Azure 基础结构作业已禁用。
  - POA 已暂时禁用了修补节点。

* 节点可能会卡在关闭状态，因为：

  - 它被手动置于关闭状态。
  - 正在进行重启（可能由 POA 触发）。
  - VM 或计算机出现故障，或网络连接问题。

### <a name="updates-were-skipped-on-some-nodes"></a>在某些节点上跳过了更新

POA 根据重新计划策略尝试安装 Windows 更新。 服务根据应用程序策略尝试恢复节点并跳过更新。

在这种情况下，将针对节点代理服务生成警告级别的运行状况报告。 Windows 更新结果还包含失败的可能原因。

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>安装更新时群集的运行状况发生错误

出现故障的 Windows 更新可以使特定节点或更新域上的应用程序或群集的运行状况下降。 POA 将停止后续 Windows 更新操作，直到群集再次正常运行。

由于 Windows 更新，管理员必须干预并确定应用程序或群集不正常的原因。

## <a name="poa-release-notes"></a>POA 发行说明

>[!NOTE]
> 对于 POA 版本1.4.0 及更高版本，你可以在 GitHub 上的[修补业务流程应用程序版本页](https://github.com/microsoft/Service-Fabric-POA/releases/)上找到发行说明和发布。

### <a name="version-110"></a>版本 1.1.0
- 公开发布的版本

### <a name="version-111"></a>版本 1.1.1
- 修复了 NodeAgentService 的 SetupEntryPoint 中的 bug，可阻止安装 NodeAgentNTService。

### <a name="version-120"></a>版本 1.2.0

- 系统重启工作流中的 Bug 修复。
- 由于修复任务准备过程中的运行状况检查，RM 任务创建过程中的 Bug 修复未能按预期方式进行。
- 已将 Windows service POANodeSvc 的启动模式从自动更改为延迟-自动。

### <a name="version-121"></a>版本 1.2.1

- 群集缩减工作流中的 Bug 修复。 引入了针对不存在节点中 POA 修复任务的垃圾回收逻辑。

### <a name="version-122"></a>版本 1.2.2

- 其他 Bug 修复。
- 二进制文件现已签名。
- 为应用程序添加了 sfpkg 链接。

### <a name="version-130"></a>版本 1.3.0

- 将 InstallWindowsOSOnlyUpdates 设置为 false 现在会安装所有可用的更新。
- 更改了禁用自动更新的逻辑。 这修复了在 Server 2016 及更高版本上不会禁用自动更新的 bug。
- 用于高级用例的 POA 的微服务的参数化放置约束。

### <a name="version-131"></a>版本 1.3.1
- 解决 POA 1.3.0 在 Windows Server 2012 R2 或更早版本上无法运行的回归，因为禁用自动更新时失败。 
- 修复了 InstallWindowsOSOnlyUpdates 配置总是被选为 True 的 bug。
- 将 InstallWindowsOSOnlyUpdates 的默认值更改为 False。

### <a name="version-132"></a>版本 1.3.2
- 修复影响节点上的修补生命周期的问题（如果有名称是当前节点名称子集的节点）。 对于此类节点，可能会丢失修补程序或等待重启。