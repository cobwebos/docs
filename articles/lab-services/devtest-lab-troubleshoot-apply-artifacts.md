---
title: 排查 Azure 开发测试实验室中项目的问题 |Microsoft Docs
description: 了解如何对在 Azure 开发测试实验室虚拟机中应用项目时出现的问题进行故障排除。
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
ms.openlocfilehash: a0505b987deb67f93de6f6166154211359515ad7
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807883"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>排查在 Azure 开发测试实验室虚拟机中应用项目时出现的问题
由于各种原因，在虚拟机上应用项目可能会失败。 本文将指导您完成一些方法，以帮助确定可能的原因。

如果在本文的任何位置需要更多帮助，可以联系 MSDN Azure 上的 Azure 开发测试实验室（DTL）专家[并 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)。 或者，你也可以提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 "获取支持"。   

> [!NOTE]
> 本文适用于 Windows 和非 Windows 虚拟机。 尽管存在一些差异，但在本文中，我们将明确说明这些差异。

## <a name="quick-troubleshooting-steps"></a>快速故障排除步骤
检查 VM 是否正在运行。 开发测试实验室要求 VM 正在运行，并且[Microsoft Azure 虚拟机代理（VM 代理）](../virtual-machines/extensions/agent-windows.md)已安装并已准备就绪。

> [!TIP]
> 在**Azure 门户**中，导航到 vm 的 "**管理项目**" 页，查看 vm 是否已准备好应用项目。 在该页的最顶部会看到一条消息。 
> 
> 使用**Azure PowerShell**检查标志**canApplyArtifacts**，此标志仅在你展开获取操作时返回。 请参阅以下示例命令：

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
可以使用以下方法之一对使用开发测试实验室创建的 Vm 进行故障排除，并使用资源管理器部署模型对其进行故障排除：

- 如果需要快速获得可能导致问题的原因，则**Azure 门户**。
- **Azure PowerShell** -如果你对 PowerShell 提示符感到满意，请使用 Azure PowerShell cmdlet 快速查询开发测试实验室资源。

> [!TIP]
> 有关如何在 VM 中查看项目执行的详细信息，请参阅[诊断实验室中的项目失败](devtest-lab-troubleshoot-artifact-failure.md)。

## <a name="symptoms-causes-and-potential-resolutions"></a>症状、原因和可能的解决方法 

### <a name="artifact-appears-to-hang"></a>项目似乎已挂起   
在预定义的超时期限到期之前，项目似乎处于挂起状态，并且项目标记为**失败**。

当某个项目看上去挂起时，首先确定其停留在何处。 在执行期间，可以在以下任一步骤中阻止项目：

- 在**初始请求期间**。 开发测试实验室创建 Azure 资源管理器模板，请求使用自定义脚本扩展（CSE）。 因此，在幕后，将触发资源组部署。 发生此级别的错误时，你将获得相关 VM 的资源组的**活动日志**中的详细信息。  
    - 你可以从 "实验室 VM" 页面导航栏访问 "活动日志"。 当你选择它时，你会看到一个条目，用于将**项目应用到虚拟机**（如果直接触发了应用项目操作）或**添加或修改虚拟机**（如果应用项目操作是 VM 创建过程的一部分）。
    - 在这些条目下查找错误。 有时不会相应地标记错误，您必须调查每个条目。
    - 调查每个条目的详细信息时，请务必查看 JSON 有效负载的内容。 你可能会在该文档的底部看到一个错误。
- **尝试执行项目时**。 这可能是由于网络或存储问题导致的。 有关详细信息，请参阅本文后面的相应部分。 这也可能是由于编写脚本的方式导致的。 例如：
    - PowerShell 脚本具有**必需的参数**，但一个参数无法向其传递值，这是因为你允许用户将其保留为空，或者因为你没有 artifactfile.json 定义文件中的属性的默认值。 由于正在等待用户输入，该脚本将挂起。
    - PowerShell 脚本需要在执行过程中**输入用户**。 必须编写脚本以无提示方式运行，无需任何用户干预。
- **VM 代理需要很长时间才能准备就绪**。 首次启动 VM 时，或首次安装自定义脚本扩展以提供应用项目的请求时，VM 可能需要升级 VM 代理或等待 VM 代理初始化。 可能存在 VM 代理所依赖的服务，这些服务需要很长时间来初始化。 在这种情况下，请参阅[Azure 虚拟机代理概述](/virtual-machines/extensions/agent-windows.md)，进一步进行故障排除。

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-script"></a>验证项目是否由于脚本而看似挂起

1. 登录到有问题的虚拟机。
2. 将该脚本复制到本地虚拟机中，或在 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>`下的虚拟机上找到它。 这是下载项目脚本的位置。
3. 使用提升的命令提示符，在本地执行脚本，同时提供用于引起问题的相同参数值。
4. 确定脚本是否受到任何不需要的行为影响。 如果是这样，则请求对该项目的更新（如果它来自公共存储库）;或者，自行进行更正（如果它来自专用存储库）。

> [!TIP]
> 你可以纠正[公共](https://github.com/Azure/azure-devtestlab)存储库中托管的项目的问题，并提交更改以供评审和批准。 请参阅[README.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md)文档中的 "**发布**" 部分。
> 
> 有关编写自己的项目的信息，请参阅[AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md)文档。

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-vm-agent"></a>若要验证由于 VM 代理，项目是否显示为挂起：
1. 登录到有问题的虚拟机。
2. 使用文件资源管理器导航到**C:\WindowsAzure\logs**。
3. 找到并打开文件**waappagent.exe**。
4. 查找显示 VM 代理启动时间和完成初始化时（即，发送第一个检测信号）的条目。 支持较新的条目，或特别是围绕您遇到此问题的时间段。

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    在此示例中，可以看到 VM 代理启动时间花了10分钟到20秒，因为发送了检测信号。 这种情况的原因是，OOBE 服务需要很长时间才能启动。

> [!TIP]
> 有关 Azure 扩展的常规信息，请参阅[azure 虚拟机扩展和功能](/virtual-machines/extensions/overview.md)。

## <a name="storage-errors"></a>存储错误
开发测试实验室需要访问为缓存项目创建的实验室存储帐户。 当开发测试实验室应用项目时，它将从配置的存储库读取项目配置及其文件。 默认情况下，开发测试实验室配置对**公共项目**存储库的访问权限。

根据 VM 的配置方式，可能无法直接访问此存储库。 因此，在设计上，开发测试实验室将项目缓存到首次初始化实验室时创建的存储帐户中。

如果以任何方式阻止了对此存储帐户的访问，则在将流量从 VM 发送到 Azure 存储服务时，可能会看到类似于以下内容的错误：

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

上述错误将显示在 "**管理项目**" 下的 "**项目结果**" 页的 "**部署消息**" 部分中。 它还将显示在相关虚拟机的资源组下的**活动日志**中。

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>确保与 Azure 存储服务的通信未被阻止：

- **检查是否添加了网络安全组（NSG）** 。 这可能是因为添加了在所有虚拟网络中自动配置 Nsg 的订阅策略。 它还会影响实验室的默认虚拟网络（如果已使用）或在实验室中配置的其他虚拟网络，用于创建 Vm。
- **检查默认实验室的存储帐户**（即创建实验室时创建的第一个存储帐户，其名称通常以字母 "a" 开头，以一个多位数数字结尾，即一个\<labname\>#）。
    1. 导航到实验室的资源组。
    2. 查找类型为的**存储帐户**的资源，该资源的名称与约定匹配。
    3. 导航到名为 "**防火墙和虚拟网络**" 的存储帐户页。
    4. 确保将其设置为 "**所有网络**"。 如果选择了 "**所选网络**" 选项，则确保将用于创建 vm 的实验室虚拟网络添加到该列表中。

有关更深入的故障排除，请参阅[配置 Azure 存储防火墙和虚拟网络](../storage/common/storage-network-security.md)。

> [!TIP]
> **验证网络安全组规则**。 使用[IP 流验证](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify)来确认网络安全组中的规则阻止了进出虚拟机的流量。 你还可以查看有效的安全组规则，以确保存在入站**允许**NSG 规则。 有关详细信息，请参阅[使用有效的安全规则排查 VM 流量流问题](../virtual-network/diagnose-network-traffic-filter-problem.md)。

## <a name="other-sources-of-error"></a>其他错误源
还有其他不太常见的错误来源。 请确保评估每个，以查看其是否适用于你的情况。 下面是其中一种： 

- **专用存储库的个人访问令牌已过期**。 当过期时，项目将不会列出，任何引用具有过期专用访问令牌的存储库中的项目的脚本都将相应失败。

## <a name="next-steps"></a>后续步骤
如果未发生上述任何错误，并且仍无法应用项目，则可以提供 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。

