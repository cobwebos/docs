---
title: 在 Azure 开发人员测试实验室中排除项目问题 |微软文档
description: 了解如何解决在 Azure DevTest Labs 虚拟机中应用项目时出现的问题。
services: devtest-lab
documentationcenter: na
author: spelluru
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: fc5051667100a2ebaa01b7815f825fadd766b08f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456990"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>在 Azure 开发人员测试实验室虚拟机中应用项目时排除问题
由于各种原因，在虚拟机上应用工件可能会失败。 本文将指导您完成一些帮助确定可能原因的方法。

如果本文中的任何一点都需要更多帮助，则可以在[MSDN Azure 和堆栈溢出论坛](https://azure.microsoft.com/support/forums/)上联系 Azure 开发人员测试实验室 （DTL） 专家。 或者，你也可以提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择"获取支持"。   

> [!NOTE]
> 本文适用于 Windows 和非 Windows 虚拟机。 虽然存在一些差异，但本文将明确调用它们。

## <a name="quick-troubleshooting-steps"></a>快速故障排除步骤
检查 VM 是否正在运行。 DevTest 实验室要求 VM 运行，并且[Microsoft Azure 虚拟机代理 （VM 代理）](../virtual-machines/extensions/agent-windows.md)已安装完毕并准备就绪。

> [!TIP]
> 在**Azure 门户**中，导航到 VM 的 **"管理项目"** 页，以查看 VM 是否已准备好应用项目。 您看到该页面顶部有一条消息。 
> 
> 使用**Azure PowerShell**，检查标志**可以应用伪影**，仅在展开 GET 操作时返回该标志。 请参阅以下示例命令：

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>故障排除方法 
您可以使用以下方法之一对使用 DevTest Labs 和资源管理器部署模型创建的 VM 进行故障排除：

- **Azure 门户**- 如果需要快速获得可能导致问题的原因的可视提示，则该门户非常棒。
- **Azure PowerShell** - 如果您对 PowerShell 提示符感到满意，请使用 Azure PowerShell cmdlet 快速查询开发人员测试实验室资源。

> [!TIP]
> 有关如何查看 VM 中的项目执行的详细信息，请参阅[在实验室中诊断项目失败](devtest-lab-troubleshoot-artifact-failure.md)。

## <a name="symptoms-causes-and-potential-resolutions"></a>症状、原因和潜在解决方法 

### <a name="artifact-appears-to-hang"></a>项目似乎挂起   
项目似乎挂起，直到预定义的超时期限过期，并且项目标记为**失败**。

当一个工件显示为挂起时，首先确定它的位置。 在执行过程中，可以在以下任一步骤中阻止项目：

- **在初始请求期间**。 开发人员测试实验室创建 Azure 资源管理器模板以请求使用自定义脚本扩展 （CSE）。 因此，在后台，将触发资源组部署。 发生此级别错误时，您将在相关 VM 的资源组**的活动日志**中获取详细信息。  
    - 可以从实验室 VM 页面导航栏访问活动日志。 选择它时，您将看到一个条目，用于**将工件应用于虚拟机**（如果应用工件操作是直接触发的）或**添加或修改虚拟机**（如果应用工件操作是 VM 创建过程的一部分）。
    - 查找这些条目下的错误。 有时，错误不会相应地标记，您必须调查每个条目。
    - 在调查每个条目的详细信息时，请确保查看 JSON 负载的内容。 您可能会在文档底部看到错误。
- **尝试执行工件 时**。 这可能是因为网络或存储问题。 有关详细信息，请参阅本文后面的相应部分。 由于脚本的创作方式，也可能发生这种情况。 例如：
    - PowerShell 脚本具有**强制参数**，但无法将值传递给它，或者因为允许用户将其留空，或者因为项目文件.json 定义文件中的属性没有默认值。 脚本将挂起，因为它正在等待用户输入。
    - PowerShell 脚本**需要用户输入**作为执行的一部分。 脚本必须编写到静默工作，而无需任何用户干预。
- **VM 代理需要很长时间才能准备就绪**。 首次启动 VM 或首次安装自定义脚本扩展以服务应用项目的请求时，VM 可能需要升级 VM 代理或等待 VM 代理初始化。 VM 代理所依赖的服务可能需要很长时间才能初始化。 在这种情况下，请参阅[Azure 虚拟机代理概述](../virtual-machines/extensions/agent-windows.md)以进行进一步的故障排除。

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-script"></a>验证项目是否由于脚本而挂起

1. 登录到有问题的虚拟机。
2. 在虚拟机中本地复制脚本，或在 下的`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>`虚拟机上找到它。 它是下载工件脚本的位置。
3. 使用提升的命令提示，在本地执行脚本，提供用于导致问题的相同参数值。
4. 确定脚本是否受到任何不需要的行为。 如果是这样，请请求对项目进行更新（如果更新来自公共存储库）;或者，自己进行更正（如果是来自您的私人回购）。

> [!TIP]
> 您可以更正我们[公开存储库](https://github.com/Azure/azure-devtestlab)中托管的工件问题，并将更改提交以供我们审核和批准。 请参阅[README.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md)文档中的 **"贡献**"部分。
> 
> 有关编写自己的工件的信息，请参阅[AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md)文档。

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-vm-agent"></a>要验证项目是否由于 VM 代理而挂起：
1. 登录到有问题的虚拟机。
2. 使用文件资源管理器导航到**C：\WindowsAzure_logs**。
3. 查找并打开文件**WaAppAgent.log**。
4. 查找显示 VM 代理何时启动以及何时完成初始化（即发送第一个检测信号）的条目。 青睐较新的条目，或者特别选择您遇到问题的时间段周围的条目。

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    在此示例中，您可以看到 VM 代理启动时间耗时 10 分钟 20 秒，因为已发送检测信号。 在这种情况下，原因就是 OOBE 服务需要很长时间才能启动。

> [!TIP]
> 有关 Azure 扩展的一般信息，请参阅[Azure 虚拟机扩展和功能](../virtual-machines/extensions/overview.md)。

## <a name="storage-errors"></a>存储错误
DevTest 实验室需要访问为缓存项目而创建的实验室存储帐户。 当 DevTest Labs 应用项目时，它将从配置的存储库中读取项目配置及其文件。 默认情况下，DevTest 实验室配置对**公共项目存储库**的访问。

根据 VM 的配置方式，它可能无法直接访问此回购。 因此，DevTest Labs 设计中缓存在首次初始化实验室时创建的存储帐户中的项目。

如果以任何方式阻止对此存储帐户的访问，例如当流量从 VM 阻止到 Azure 存储服务时，您可能会看到类似于以下错误：

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

上述错误将显示在"管理项目 **"下的"项目结果**"页中的 **"部署消息****"部分中**。 它还将显示在相关虚拟机的资源组下**的活动日志**中。

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>为确保未阻止与 Azure 存储服务的通信，

- **检查已添加的网络安全组 （NSG）。** 可能是添加了订阅策略，其中 NSG 在所有虚拟网络中自动配置。 它还会影响实验室的默认虚拟网络（如果使用）或实验室中配置用于创建 VM 的其他虚拟网络。
- **检查默认实验室的存储帐户**（即创建实验室时创建的第一个\<存储帐户，其名称通常以字母"a"开头，以多位数数字即实验室名称\>*结尾）。
    1. 导航到实验室的资源组。
    2. 查找类型**存储帐户**的资源，其名称与约定匹配。
    3. 导航到名为**防火墙和虚拟网络的**存储帐户页面。
    4. 确保将其设置为 **"所有网络**"。 如果选择了 **"选定网络**"选项，则确保将用于创建 VM 的实验室虚拟网络添加到列表中。

有关更深入的故障排除，请参阅[配置 Azure 存储防火墙和虚拟网络](../storage/common/storage-network-security.md)。

> [!TIP]
> **验证网络安全组规则**。 使用[IP 流验证](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify)确认网络安全组中的规则是否阻止进出虚拟机的流量。 您还可以查看有效的安全组规则，以确保存在入站**允许**NSG 规则。 有关详细信息，请参阅[使用有效的安全规则排查 VM 流量流问题](../virtual-network/diagnose-network-traffic-filter-problem.md)。

## <a name="other-sources-of-error"></a>其他错误来源
还有其他不太常见的错误源。 请确保评估每个情况，以查看其是否适用于您的案例。 下面是其中之一： 

- **已过期的个人访问令牌为专用回购**。 过期后，项目将不会列出，并且引用具有过期私有访问令牌的存储库中的工件的任何脚本都将相应失败。

## <a name="next-steps"></a>后续步骤
如果这些错误均未发生，但仍无法应用项目，则可以提交 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。

