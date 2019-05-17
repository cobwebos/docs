---
title: 通过 Azure 策略为 Vm 启用 Azure 监视器 |Microsoft Docs
description: 本文介绍如何在 Azure Monitor 为 Vm 启用为多个 Azure 虚拟机或虚拟机规模集使用 Azure 策略。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: magoedte
ms.openlocfilehash: 9664ad5272ffeb55bd85e3d4c4f4b70d05e97702
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524140"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-policy"></a>为 Vm （预览版） 使用 Azure 策略中启用 Azure Monitor

本文介绍如何为 Azure 虚拟机或虚拟机规模集使用 Azure 策略的 Vm （预览版） 中启用 Azure Monitor。 在此过程结束时，将已成功配置启用 Log Analytics 和依赖关系代理，并识别哪些虚拟机将不符合策略。

若要发现、 管理和 Azure Monitor 启用适用于 Azure 虚拟机或虚拟机规模集的所有 Vm，可以使用 Azure 策略或 Azure PowerShell。 Azure 策略是推荐的方法，因为你可以管理策略定义来有效地管理你的订阅以确保一致的符合性和自动启用的新预配 Vm。 这些策略定义：

* 部署 Log Analytics 代理和 Dependency Agent。
* 报告合规性结果。
* 修正不合规的 VM。

如果您有兴趣实现这个目的使用 Azure PowerShell 或 Azure 资源管理器模板，请参阅[使用 Azure PowerShell 或 Resource Manager 模板启用](vminsights-enable-at-scale-powershell.md)。 

## <a name="manage-policy-coverage-feature-overview"></a>管理策略覆盖范围功能概述

最初从 Azure 策略以独占方式执行与 Azure 策略的管理和部署 Vm 的 Azure Monitor 的策略定义的体验。 与**管理策略覆盖率**功能，它使它更简单并且更轻松地发现、 管理和启用大规模**启用 Vm 的 Azure Monitor**计划，其中包括的策略定义前面所述。 您可以访问此新功能**开始**Vm 的 Azure Monitor 中通过选择的选项卡**管理策略覆盖范围**。 它会打开 Vm 策略覆盖范围页。 

![从 Vm 开始选项卡上的 azure 监视器](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

从此处你可以检查和跨管理组和订阅，管理为该计划的覆盖范围，以及了解多少个 Vm 位于每个管理组和订阅以及其符合性状态。   

![Azure 监视的 Vm 管理策略页](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

此信息可用于规划和管理方案的 Azure Monitor 执行 vm 从一个中心位置。 虽然策略/计划分配给作用域时，Azure 策略提供了符合性视图，使用这一新页面可以发现其中策略/计划未分配，并将其分配到当前位置。 所有操作 （分配、 查看、 编辑） 直接重定向到 Azure 策略。 Azure 监视的 Vm 策略覆盖范围页是仅在计划的扩展和集成体验**启用 Vm 的 Azure Monitor**。 

从此页还可以配置 Log Analytics 工作区的 Azure Monitor 为虚拟机，后者将执行以下：

- 安装安装服务映射和基础结构见解解决方案
- 启用性能图表、 工作簿和您的自定义日志查询和警报使用的操作系统性能计数器。

![Azure 监视的 Vm 配置工作区](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

此选项不与任何策略操作，可用于提供满足的简单办法[先决条件](vminsights-enable-overview.md)为 Vm 启用 Azure Monitor 的必要条件。  

### <a name="what-information-is-available-on-this-page"></a>此页上提供了哪些信息？
下表提供了策略覆盖率该页中显示哪些信息以及如何解释它的细分。

| 函数 | 描述 | 
|----------|-------------| 
| **范围** | 管理组并且您具有的订阅或继承功能向下钻取通过管理组层次结构的访问权限。|
| **角色** | 你的角色到作用域，可能是读取器所有者或参与者。 在某些情况下，它可能显示为空白，以指示可能有权访问订阅，但不是到管理组它属于。 因为它是在确定哪些数据可以看到和可执行根据分配计划/策略 （所有者），编辑它们，或查看符合性的操作的密钥，根据你的角色会有所不同中其他列的信息。 |
| **总虚拟机** | 该范围内的 Vm 数。 为管理组，它是 Vm 的订阅和/或子管理组下嵌套的总和。 |
| **分配覆盖率** | 计划/策略涵盖的 Vm 的百分比。 |
| **分配状态** | 在本专栏中，您可以找到的状态信息的策略 / 计划分配。 |
| **兼容的 Vm** | 策略/计划下的符合的 Vm 数。 为该计划**启用 Vm 的 Azure Monitor**这是具有 Log Analytics 代理和依赖关系代理的 Vm 数。 在某些情况下，它可能显示为空白由于没有赋值，或者没有 Vm 或没有足够的权限。 在符合性状态下提供的信息。 |
| **合规性** | 符合性的总数是不同的资源符合划分的所有不同资源的总和的总和。 |
| **符合性状态** | 你的策略的符合性状态的信息 / 计划分配。|

分配计划/策略后，选择分配中的作用域可能是列出该作用域或其子集。 例如，你可能已创建订阅 （策略作用域） 而不是管理组 （覆盖范围） 的工作分配。 在本示例中的值**分配覆盖率**将指示除以覆盖率作用域中的 Vm 的策略 （或计划） 中的 Vm 作用域。 在另一种情况下，您可能有一些虚拟机、 资源组或订阅从排除策略作用域。 如果值为空，则表示的策略/计划不存在或您没有权限 （在分配状态中提供的信息）。

## <a name="enable-using-azure-policy"></a>使用 Azure Policy 启用

若要在租户中使用 Azure Policy 启用用于 VM 的 Azure Monitor，请执行以下操作：

- 将计划分配到范围：管理组、订阅或资源组
- 检查和修正合规性结果

有关分配 Azure Policy 的详细信息，请参阅 [Azure Policy 概述](../../governance/policy/overview.md#policy-assignment)，并在继续操作前查看[管理组概述](../../governance/management-groups/index.md)。

### <a name="policies-for-azure-vms"></a>适用于 Azure Vm 的策略

下表列出了 Azure VM 的策略定义：

|名称 |描述 |Type |
|-----|------------|-----|
|[预览]：启用用于 VM 的 Azure Monitor |在指定范围（管理组、订阅或资源组）中启用用于虚拟机 (VM) 的 Azure Monitor。 将 Log Analytics 工作区用作参数。 |计划 |
|[预览]：审核 Dependency Agent 部署 — VM 映像 (OS) 未列出 |如果 VM 映像 (OS) 未在列表中定义且未安装代理，则报告 VM 不合规。 |策略 |
|[预览]：审核 Log Analytics 代理部署 — VM 映像 (OS) 未列出 |如果 VM 映像 (OS) 未在列表中定义且未安装代理，则报告 VM 不合规。 |策略 |
|[预览]：为 Linux VM 部署 Dependency Agent |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Linux VM 部署 Dependency Agent。 |策略 |
|[预览]：为 Windows VM 部署 Dependency Agent |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Windows VM 部署 Dependency Agent。 |策略 |
|[预览]：为 Linux VM 部署 Log Analytics 代理 |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Linux VM 部署 Log Analytics 代理。 |策略 |
|[预览]：为 Windows VM 部署 Log Analytics 代理 |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Windows VM 部署 Log Analytics 代理。 |策略 |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>策略的 Azure 虚拟机规模集

下表列出了为 Azure 虚拟机规模集的策略定义：

|名称 |描述 |Type |
|-----|------------|-----|
|[预览]：启用 Azure VM 规模集 (VMSS) 监视器 |为虚拟机规模集 （管理组、 订阅或资源组） 指定的作用域中启用 Azure Monitor。 将 Log Analytics 工作区用作参数。 注意： 如果在规模集的 upgradePolicy 设置为手动，需要通过在其上调用升级将扩展应用到集中的所有 Vm。 在 CLI 中，这将是 az vmss 更新实例。 |计划 |
|[预览]：审核在 VMSS – VM 映像 (OS) 未列出的依赖项代理部署 |报告虚拟机规模集作为不兼容 VM 映像 (OS) 并不定义在列表中并且代理未安装。 |策略 |
|[预览]：在 VMSS – VM 映像 (OS) 未列出的审核日志分析代理部署 |报告虚拟机规模集作为不兼容 VM 映像 (OS) 并不定义在列表中并且代理未安装。 |策略 |
|[预览]：部署 Linux VM 规模集 (VMSS) 的依赖关系代理 |为 Linux 虚拟机规模集如果在列表中定义 VM 映像 (OS)，并且未安装代理部署的依赖关系代理。 |策略 |
|[预览]：部署 Windows VM 规模集 (VMSS) 的依赖关系代理 |部署依赖关系代理的 Windows 虚拟机规模集如果在列表中定义 VM 映像 (OS)，并且未安装代理。 |策略 |
|[预览]：为 Linux VM 规模集(VMSS)部署 Log Analytics 代理 |将 Log Analytics 代理部署适用于 Linux 虚拟机规模集如果在列表中定义 VM 映像 (OS)，并且未安装代理。 |策略 |
|[预览]：为 Windows VM 规模集(VMSS)部署 Log Analytics 代理 |部署日志分析代理的 Windows 虚拟机规模集如果在列表中定义 VM 映像 (OS)，并且未安装代理。 |策略 |

以下介绍独立策略（未包含在计划中）：

|名称 |描述 |Type |
|-----|------------|-----|
|[预览]：审核 VM 的 Log Analytics 工作区 — 报告不匹配 |如果 VM 未登录到策略/计划分配中指定的 Log Analytics 工作区，则报告 VM 不合规。 |策略 |

### <a name="assign-the-azure-monitor-initiative"></a>分配 Azure Monitor 计划
若要从 Vm 策略覆盖范围页 Azure Monitor 创建策略分配，请执行以下步骤。 若要了解如何完成这些步骤，请参阅 [从 Azure 门户创建策略分配](../../governance/policy/assign-policy-portal.md)。

分配计划/策略后，选择分配中的作用域可能是此处列出的作用域或其子集。 例如，你可能已创建订阅 （策略作用域） 的分配和不是管理组 （覆盖范围） 大小写的覆盖率 %将在其中指示除以覆盖率作用域中的 Vm 的策略 （或计划） 中的 Vm 作用域。 在某些其他情况下，您可能有一些 Vm 或 RGs 或订阅从排除策略作用域。  如果为空，它指示两个策略 / 计划不存在或您没有权限 （分配状态中提供的信息）  

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在 Azure 门户中选择“监视”。 

3. 在“见解”分区中选择“虚拟机(预览)”。
 
4. 选择**开始**选项卡，并在页面上选择**管理策略覆盖范围**。

5. 从表中，选择管理组或订阅，然后选择**作用域**通过单击省略号 （...）。    在我们的示例中，范围将策略分配限制为用于执行的一组虚拟机。

6. 上**Azure 策略分配**预先填入计划页**启用 Vm 的 Azure Monitor**。 
    **分配名称**框自动填充具有计划的名称，但您可以更改它。 还可添加可选的说明。 “分配者”框根据登录的用户自动填充，此值为可选值。

7. （可选）若要从范围中删除一个或多个资源，请选择“排除项”。

8. 在受支持区域的“Log Analytics 工作区”下拉列表中，选择一个工作区。

   > [!NOTE]
   > 如果工作区超出分配范围，则将 *Log Analytics 参与者*权限授予策略分配的主体 ID。 如果不这样做，可能会看到部署失败，例如：`The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`若要授予访问权限，请查看[如何手动配置托管标识](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)。
   > 
   >  “托管标识”复选框会被选中，因为要分配的计划包含具有 *deployIfNotExists* 效果的策略。
    
9. 在“管理标识位置”下拉列表中，选择适当的区域。

10. 选择“分配”。

后创建分配的 Azure 监视的 Vm 策略覆盖范围页将更新分配覆盖率、 分配状态、 符合性的 Vm 和符合性状态，以反映所做的更改。 

以下矩阵映射每个可能符合性状态的计划。  

| 符合性状态 | 描述 | 
|------------------|-------------|
| **符合** | 作用域中的所有 Vm 都都部署到他们的 Log Analytics 和依赖关系代理。|
| **不符合** | 作用域中的并非所有 Vm 都均存储在 Log Analytics 和依赖关系代理部署到它们，并且可能需要修正。|
| **未启动** | 添加了新的分配。 |
| **Lock** | 没有足够的特权来管理组。<sup>1</sup> | 
| **空白** | 没有分配的策略。 | 

<sup>1</sup>如果没有对管理组访问权限，要求提供访问或查看符合性和通过子管理组或订阅管理分配的所有者。 

下表列出了每个可能的分配状态的计划。

| 分配状态 | 描述 | 
|------------------|-------------|
| **Success** | 作用域中的所有 Vm 都都部署到他们的 Log Analytics 和依赖关系代理。|
| **警告** | 该订阅不是管理组下。|
| **未启动** | 添加了新的分配。 |
| **Lock** | 没有足够的特权来管理组。<sup>1</sup> | 
| **空白** | 没有虚拟机存在，或者未分配策略。 | 
| **Action** | 分配策略或编辑分配 | 

<sup>1</sup>如果没有对管理组访问权限，要求提供访问或查看符合性和通过子管理组或订阅管理分配的所有者。 

## <a name="review-and-remediate-the-compliance-results"></a>检查和修正合规结果

下面的示例适用于 Azure VM，但也适用于虚拟机规模集。 可阅读[识别不合规结果](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)一文，了解如何检查合规结果。 在 Vm 策略覆盖范围页 Azure Monitor，从表中，选择管理组或订阅，并选择**查看符合性**通过单击省略号 （...）。   

![Azure VM 的策略合规性](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

根据计划中包含的策略的结果，VM 在以下情况中报告为不合规：

* 未部署 Log Analytics 代理或 Dependency Agent。  
    这对于已经拥有 VM 的范围来说是典型情况。 若要解决此问题，请在不合规的策略上[创建修正任务](../../governance/policy/how-to/remediate-resources.md)来部署所需的代理。  
    - [预览]: Deploy Dependency Agent for Linux VMs
    - [预览]: Deploy Dependency Agent for Windows VMs
    - [预览]: Deploy Log Analytics Agent for Linux VMs
    - [预览]: Deploy Log Analytics Agent for Windows VMs

* VM 映像 (OS) 未在策略定义中确定。  
    部署策略标准仅包含通过已知 Azure VM 映像部署的 VM。 请查看文档，了解 VM OS 是否受支持。 如果不受支持，则复制部署策略并更新或修改它来使映像合规。  
    - [预览]：审核 Dependency Agent 部署 — VM 映像 (OS) 未列出
    - [预览]：审核 Log Analytics 代理部署 — VM 映像 (OS) 未列出

* VM 未登录到指定的 Log Analytics 工作区。  
    可能存在计划范围中的某些 VM 登录到策略部署中指定的 Log Analytics 工作区以外的 Log Analytics 工作区的情况。 此策略是用于确定向不合规工作区报告的 VM 的工具。  
    - [预览]: Audit Log Analytics Workspace for VM - Report Mismatch

## <a name="edit-initiative-assignment"></a>编辑计划分配

在分配到管理组或订阅的计划后，随时可以编辑它以修改以下属性：

- 分配名称
- 描述
- 分配者
- Log Analytics 工作区
- 例外

## <a name="next-steps"></a>后续步骤

现在，你的虚拟机启用监视，此信息是可用于分析的 Vm 的 Azure monitor。 若要了解如何使用运行状况功能，请参阅[查看用于 VM 的 Azure Monitor 的运行状况](vminsights-health.md)。 若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。 若要查明 VM 性能的瓶颈和整体利用率，请参阅[查看 Azure VM 性能](vminsights-performance.md)；若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。