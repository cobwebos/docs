---
title: "自动缩放 HPC Pack 群集节点 | Microsoft Docs"
description: "自动扩展和收缩 Azure 中的 HPC Pack 群集计算节点数"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: 
editor: tysonn
ms.assetid: 38762cd1-f917-464c-ae5d-b02b1eb21e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/08/2016
ms.author: danlep
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 0dc0d15c64d8951c3c457df73588c37418a3c8a4
ms.contentlocale: zh-cn
ms.lasthandoff: 03/25/2017

---
# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>在 Azure 中根据群集工作负荷自动扩展和收缩 HPC Pack 群集资源
如果在 HPC Pack 群集中部署 Azure“突发”节点，或者在 Azure VM 中创建 HPC Pack 群集，可能希望有一种方法能够根据群集上的工作负荷自动扩展或收缩群集资源（例如节点或核心）。 以这种方式缩放群集资源，可更有效地使用 Azure 资源并控制其成本。

本文说明 HPC Pack 提供用于自动缩放计算资源的两种方法：

* HPC Pack 群集属性 **AutoGrowShrink**

* **AzureAutoGrowShrink.ps1** HPC PowerShell 脚本

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

当前只能自动扩展和收缩正在运行 Windows Server 操作系统的 HPC Pack 计算节点。


## <a name="set-the-autogrowshrink-cluster-property"></a>设置 AutoGrowShrink 群集属性
### <a name="prerequisites"></a>先决条件

* **HPC Pack 2012 R2 Update 2 或更高版群集** - 群集头节点既可以部署在本地，也可以部署在 Azure VM 中。 请参阅[使用 HPC Pack 设置一个混合群集](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)，开始使用本地头节点和 Azure“突发”节点。 若要在 Azure VM 中快速部署 HPC Pack 群集，请参阅 [HPC Pack IaaS deployment script](hpcpack-cluster-powershell-script.md)（HPC Pack IaaS 部署脚本）。

* **对于 Azure 中包含头节点的群集（Resource Manager 部署模型）** - 从 HPC Pack 2016 开始，Azure Active Directory 应用程序中的证书身份验证用于自动扩展和收缩使用 Azure Resource Manager 部署的群集 VM。 配置证书，如下所示：

  1. 部署群集后，远程桌面将连接到一个头节点。

  2. 将证书（带私钥的 PFX 格式）上载到每个头节点并安装到 Cert:\LocalMachine\My 和 Cert:\LocalMachine\Root。

  3. 以管理员身份启动 Azure PowerShell，并在一个头节点上运行以下命令：

    ```powershell
        cd $env:CCP_HOME\bin

        Login-AzureRmAccount
    ```
        
    如果帐户存在于多个 Azure Active Directory 租户或 Azure 订阅中，则可以运行以下命令来选择正确的租户和订阅：
  
    ```powershell
        Login-AzureRMAccount -TenantId <TenantId> -SubscriptionId <subscriptionId>
    ```     
       
    运行以下命令来查看当前所选的租户和订阅：
    
    ```powershell
        Get-AzureRMContext
    ```

  4. 运行以下脚本

    ```powershell
        .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -CertificateThumbprint "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" -TenantId xxxxxxxx-xxxxx-xxxxx-xxxxx-xxxxxxxxxxxx
    ```

    其中

    **DisplayName** - Azure Active 应用程序的显示名称。 如果该应用程序不存在，将在 Azure Active Directory 中创建它。

    **主页** - 应用程序的主页。 可以配置虚拟 URL，如前面的示例所示。

    **IdentifierUri** - 应用程序的标识符。 可以配置虚拟 URL，如前面的示例所示。

    **CertificateThumbprint** - 步骤 1 中在头节点上安装的证书的指纹。

    **TenantId** - Azure Active Directory 的租户 ID。 可以从 Azure Active Directory 门户“属性”页获取租户 ID。

    如需 **ConfigARMAutoGrowShrinkCert.ps1** 的详细信息，请运行 `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`。


* **对于 Azure 中包含头节点的群集（经典部署模型）** - 如果使用 HPC Pack IaaS 部署脚本在经典部署模型中创建群集，请通过在群集配置文件中设置 AutoGrowShrink 选项来启用 **AutoGrowShrink** 群集属性。 有关详细信息，请参阅[下载的脚本](https://www.microsoft.com/download/details.aspx?id=44949)随附的文档。

    或者，使用下一部分所述的 HPC PowerShell 命令在部署群集后启用 **AutoGrowShrink** 群集属性。 若要对此做好准备，先完成以下步骤：

  1. 在头节点和 Azure 订阅上配置 Azure 管理证书。 对于测试部署，可以使用 HPC Pack 在头节点上安装的默认 Microsoft HPC Azure 自签名证书，然后将该证书上载到 Azure 订阅。 有关选项和步骤，请参阅 [TechNet 库指南](https://technet.microsoft.com/library/gg481759.aspx)。

  2. 在头节点上运行 **regedit**，转到 HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo，然后添加字符串值。 将“值”名称设置为“ThumbPrint”，将“值”数据设置为步骤 1 中的证书指纹。

### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>用于设置 AutoGrowShrink 属性的 HPC PowerShell 命令
下面是 HPC PowerShell 命令示例，它们可以设置 **AutoGrowShrink** 并使用其他参数来优化其行为。 请参阅本文稍后的 [AutoGrowShrink 参数](#AutoGrowShrink-parameters)以获取完整的设置列表。

若要运行这些命令，请在群集头节点上以管理员身份启动 HPC PowerShell。

**启用 AutoGrowShrink 属性**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 1
```

**禁用 AutoGrowShrink 属性**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 0
```

**更改扩展间隔（以分钟为单位）**

```powershell
Set-HpcClusterProperty –GrowInterval <interval>
```

**更改收缩间隔（以分钟为单位）**

```powershell
Set-HpcClusterProperty –ShrinkInterval <interval>
```

**查看 AutoGrowShrink 的当前配置**

```powershell
Get-HpcClusterProperty –AutoGrowShrink
```

**从 AutoGrowShrink 中排除节点组**

```powershell
Set-HpcClusterProperty –ExcludeNodeGroups <group1,group2,group3>
```

>[!NOTE] 
> 从 HPC Pack 2016 开始支持此参数
>

### <a name="autogrowshrink-parameters"></a>AutoGrowShrink 参数
下面是可以使用 **Set-HpcClusterProperty** 命令修改的 AutoGrowShrink 参数。

* **EnableGrowShrink** - 用于启用或禁用 **AutoGrowShrink** 属性的开关。
* **ParamSweepTasksPerCore** - 用于扩展一个核心的参数扫描任务数目。 默认为每个任务扩展一个核心。

  > [!NOTE]
  > HPC Pack QFE KB3134307 将 **ParamSweepTasksPerCore** 更改为 **TasksPerResourceUnit**。 它基于作业资源类型，并且可以是节点、套接字或核心。
  >
  >
* **GrowThreshold** - 用于触发自动扩展的排队任务的阈值。 默认值为 1，这意味着，如果有 1 个或多个任务处于排队状态，将自动扩展节点。
* **GrowInterval** - 触发自动扩展的间隔（以分钟为单位）。 默认间隔为 5 分钟。
* **ShrinkInterval** - 触发自动收缩的间隔（以分钟为单位）。 默认间隔为 5 分钟。|
* **ShrinkIdleTimes** - 指示节点为空闲状态之前，持续检查收缩的次数。 默认值为 3 次。 例如，如果 **ShrinkInterval** 为 5 分钟，HPC Pack 将每隔 5 分钟检查一次节点是否处于空闲状态。 如果节点连续 3 次检查（15 分钟）都处于空闲状态，HPC Pack 将收缩该节点。
* **ExtraNodesGrowRatio** - 要为消息传递接口 (MPI) 作业扩展的节点附加百分比。 默认值为 1，表示 HPC Pack 针对 MPI 作业扩展节点 1%。
* **GrowByMin** - 用于指示自动扩展策略是否基于作业所需的最少资源的开关。 默认值为 false，这意味着 HPC Pack 将基于作业所需的资源数上限为作业扩展节点。
* **SoaJobGrowThreshold** - 传入 SOA 请求以触发自动扩展过程的阈值。 默认值为 50000。

  > [!NOTE]
  > 从 HPC Pack 2012 R2 Update 3 开始支持此参数。
  >
  >
* **SoaRequestsPerCore** - 用于扩展一个核心的传入 SOA 请求数目。 默认值为 20000。

  > [!NOTE]
  > 从 HPC Pack 2012 R2 Update 3 开始支持此参数。
  >
* **ExcludeNodeGroups** - 指定的节点组中的节点不会自动扩展和收缩。
  
  > [!NOTE]
  > 从 HPC Pack 2016 开始支持此参数。
  >

### <a name="mpi-example"></a>MPI 示例
默认情况下，HPC Pack 针对 MPI 作业额外扩展 1% 的节点（**ExtraNodesGrowRatio** 设置为 1）。 原因是 MPI 可能需要多个节点，且只有在准备好所有节点时才能运行该作业。 当 Azure 启动节点时，一个节点偶尔可能需要比其他节点更多的时间才能启动，使得其他节点在等待该节点就绪前空闲。 通过扩展附加节点，HPC Pack 可减少此资源等待时间，并可能会节省成本。 若要扩展 MPI 操作的附加节点百分比（例如，10%），请运行类似于下面的命令

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>SOA 示例
默认情况下，**SoaJobGrowThreshold** 设置为 50000，**SoaRequestsPerCore** 设置为 200000。 如果提交一个包含 70000 个请求的 SOA 作业，则会有一个排队任务，并且传入请求数为 70000。 在此情况下，HPC Pack 将会针对排队任务扩展 1 个核心，并针对传入请求扩展 (70000 - 50000)/20000 = 1 个核心，因此总共为此 SOA 作业扩展了 2 个核心。

## <a name="run-the-azureautogrowshrinkps1-script"></a>运行 AzureAutoGrowShrink.ps1 脚本
### <a name="prerequisites"></a>先决条件

* **HPC Pack 2012 R2 Update 1 或更高版本群集** - **AzureAutoGrowShrink.ps1** 脚本已安装在 %CCP_HOME%bin 文件夹中。 群集头节点既可以部署在本地，也可以部署在 Azure VM 中。 请参阅[使用 HPC Pack 设置一个混合群集](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)，开始使用本地头节点和 Azure“突发”节点。 若要在 Azure VM 中快速部署 HPC Pack 群集，请参阅 [HPC Pack IaaS deployment script](hpcpack-cluster-powershell-script.md)（HPC Pack IaaS 部署脚本），或使用 [Azure 快速入门模板](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)。
* **Azure PowerShell 1.4.0** - 该脚本当前依赖于此特定版本的 Azure PowerShell。
* **对于具有 Azure 突发节点的群集** - 在安装了 HPC Pack 的客户端计算机上或在头节点上运行脚本。 如果在客户端计算机上运行，请确保将变量 $env:CCP_SCHEDULER 设置为指向头节点。 Azure“突发”节点必须已添加到群集，但是其可能处于“未部署”状态。
* **对于 Azure VM（Resource Manager 部署模型）中部署的群集** - 对于 Resource Manager 部署模型中部署的 Azure VM 群集，该脚本支持两种 Azure 身份验证方法：通过运行 `Login-AzureRmAccount` 登录到 Azure 帐户以每次运行该脚本，或者将服务主体配置为使用证书进行身份验证。 HPC Pack 提供了脚本 **ConfigARMAutoGrowShrinkCert.ps**，用于使用证书创建服务主体。 此脚本将创建 Azure Active Directory (Azure AD) 应用程序和服务主体，并将参与者角色分配给服务主体。 若要运行脚本，以管理员身份启动 Azure PowerShell 并运行以下命令：

    ```powershell
    cd $env:CCP_HOME\bin

    Login-AzureRmAccount

    .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -PfxFile "d:\yourcertificate.pfx"
    ```

    如需 **ConfigARMAutoGrowShrinkCert.ps1** 的详细信息，请运行 `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`。

* **对于 Azure VM（经典部署模型）中部署的群集** - 请在头节点 VM 上运行脚本，因为相关的 **Start-HpcIaaSNode.ps1** 和 **Stop-HpcIaaSNode.ps1** 脚本安装在这个位置。 这些脚本还需要 Azure 管理证书或发布设置文件（请参阅[管理 Azure 的 HPC Pack 群集中的计算节点](hpcpack-cluster-node-manage.md)）。 请确保你需要的所有计算节点 VM 都已添加到群集。 它们可能处于停止状态。



### <a name="syntax"></a>语法
```powershell
AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfActiveQueuedTasksPerNodeToGrow <Single> [-NumOfActiveQueuedTasksToGrowThreshold <Int32>]
    [-NumOfInitialNodesToGrow <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>]
    [-ShrinkCheckIdleTimes <Int32>] [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>]
    [<CommonParameters>]

AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfQueuedJobsPerNodeToGrow <Single> [-NumOfQueuedJobsToGrowThreshold <Int32>] [-NumOfInitialNodesToGrow
    <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>] [-ShrinkCheckIdleTimes <Int32>]
    [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]

AzureAutoGrowShrink.ps1 -UseLastConfigurations [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]
```
### <a name="parameters"></a>parameters
* **NodeTemplates** - 节点模板的名称，可定义节点扩展和收缩的范围。 如果未指定（默认值是 @()），则在 **NodeType** 的值为 AzureNodes 时，**AzureNodes** 节点组中的所有节点都在范围内，在 **NodeType** 的值为 ComputeNodes 时，**ComputeNodes** 节点组中的所有节点都在范围内。
* **JobTemplates** - 作业模板的名称，可定义节点扩展的范围。
* **NodeType** - 要扩展和收缩的节点类型。 支持的值是：

  * **AzureNodes** - 适用于本地群集或 Azure IaaS 群集中的 Azure PaaS（迸发）节点。
  * **ComputeNodes** - 仅适用于 Azure IaaS 群集中的计算节点 VM。

* **NumOfQueuedJobsPerNodeToGrow** - 要扩展一个节点所需的已排队作业数量。
* **NumOfQueuedJobsToGrowThreshold** - 要启动扩展过程所需的已排队作业的阈值数量。
* **NumOfActiveQueuedTasksPerNodeToGrow** - 要扩展一个节点所需的活动已排队任务数量。 如果 **NumOfQueuedJobsPerNodeToGrow** 指定为大于 0 的值，则忽略此参数。
* **NumOfActiveQueuedTasksToGrowThreshold** - 要启动扩展过程所需的活动已排队任务的阈值数量。
* **NumOfInitialNodesToGrow** - 当范围内的所有节点都处于“未部署”或“已停止（已解除分配）”状态时，要扩展节点的初始最小数量。
* **GrowCheckIntervalMins** - 扩展检查之间的间隔（以分钟为单位）。
* **ShrinkCheckIntervalMins** - 收缩检查之间的间隔（以分钟为单位）。
* **ShrinkCheckIdleTimes** - 在指示节点处于空闲状态之前，连续收缩检查（由 **ShrinkCheckIntervalMins** 分隔）的次数。
* **UseLastConfigurations** - 以前的配置保存在参数文件中。
* **ArgFile** - 用于保存和更新运行脚本的配置的参数文件名称。
* **LogFilePrefix** - 日志文件的前缀名称。 你可以指定一个路径。 默认情况下，日志将写入当前工作目录。

### <a name="example-1"></a>示例 1
下面的示例将使用“默认 AzureNode 模板”部署的 Azure 突发节点配置为自动增加和减少。 如果所有节点最初都处于“未部署”状态，则至少启动了 3 个节点。 如果已排队作业的数量超过 8 个，则脚本会启动节点，直至节点数量超过已排队作业数与 **NumOfQueuedJobsPerNodeToGrow** 的比。 如果发现一个节点连续 3 次闲置，则会停止此节点。

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>示例 2
下面的示例将使用“默认 ComputeNode 模板”部署的 Azure 计算节点 VM 配置为自动增加和减少。
由默认作业模板配置的作业可定义群集上工作负荷的范围。 如果所有节点最初都处于已停止状态，则至少启动了 5 个节点。 如果活动已排队任务的数量超过 15 个，则脚本会启动节点，直至节点数量超过活动已排队任务数与 **NumOfActiveQueuedTasksPerNodeToGrow** 的比。 如果发现一个节点连续 10 次闲置，则会停止此节点。

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```

