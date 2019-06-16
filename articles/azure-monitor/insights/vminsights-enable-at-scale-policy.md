---
title: 使用 Azure 策略适用于 Vm 中启用 Azure Monitor |Microsoft Docs
description: 本文介绍如何为 Vm 的 Azure Monitor 启用多个 Azure 虚拟机或虚拟机规模集使用 Azure 策略。
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
ms.openlocfilehash: fda79a7ea361a6b44798d18b79ffd763055087a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122628"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>使用 Azure 策略的 Vm （预览版） 中启用 Azure Monitor

本文介绍如何为 Vm （预览版） 中启用 Azure Monitor 的 Azure 虚拟机或虚拟机规模集使用 Azure 策略。 在此过程结束时，将成功地配置启用 Log Analytics 和依赖关系代理和标识并不符合的虚拟机。

若要发现、 管理和 Azure Monitor 启用适用于 Azure 虚拟机或虚拟机规模集的所有 Vm，可以使用 Azure 策略或 Azure PowerShell。 Azure 策略是可以管理策略定义来有效地管理你的订阅以确保一致的符合性并且自动启用的新预配 Vm，因此我们建议的方法。 这些策略定义：

* 部署 Log Analytics 代理和 Dependency Agent。
* 报告合规性结果。
* 修正的不符合要求的 Vm。

如果有兴趣完成这些任务使用 Azure PowerShell 或 Azure 资源管理器模板，请参阅[的 Vm （预览版） 使用 Azure PowerShell 或 Azure 资源管理器模板中启用 Azure Monitor](vminsights-enable-at-scale-powershell.md)。

## <a name="manage-policy-coverage-feature-overview"></a>管理策略覆盖范围功能概述

最初，Azure 策略的管理和部署 Vm 的 Azure Monitor 的策略定义的体验是从 Azure 策略以独占方式完成的。 管理策略覆盖范围功能使它更简单并且更轻松地发现、 管理和启用大规模**启用 Vm 的 Azure Monitor**计划，其中包括前面提到的策略定义。 您可以访问此新功能**开始**Vm 的 Azure Monitor 中的选项卡。 选择**管理策略覆盖率**以打开**Vm 策略覆盖范围的 Azure Monitor**页。

![从 Vm 开始选项卡上的 azure 监视器](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

在这里，您可以检查和跨管理组和订阅管理覆盖率为该计划。 您可以了解多少个 Vm 位于每个管理组和订阅以及其符合性状态。

![Azure 监视的 Vm 管理策略页](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

此信息可帮助您规划和管理方案的 Azure Monitor 执行 vm 从一个中心位置。 虽然策略或计划分配给作用域时，Azure 策略提供了符合性视图，使用此新页您可以发现其中策略或计划未分配，并将它分配到位。 分配、 查看和直接编辑重定向到 Azure 策略之类的所有操作。 **Vm 策略覆盖范围的 Azure Monitor**页是仅在计划的扩展和集成体验**启用 Vm 的 Azure Monitor**。

从此页中，还可以配置 Log Analytics 工作区的 Azure Monitor 的 Vm，其中：

- 安装安装服务映射和基础结构见解解决方案。
- 启用性能图表、 工作簿和您的自定义日志查询和警报使用的操作系统性能计数器。

![Azure 监视的 Vm 配置工作区](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

此选项不与任何策略操作。 是可用于提供满足的简单办法[先决条件](vminsights-enable-overview.md)为 Vm 启用 Azure Monitor 的必要条件。  

### <a name="what-information-is-available-on-this-page"></a>此页上提供了哪些信息？
下表提供策略覆盖率页以及如何解释它所显示的信息的细分。

| 函数 | 描述 | 
|----------|-------------| 
| **范围** | 管理组并且您具有的订阅或继承与管理组层次结构向下钻取功能的访问权限。|
| **角色** | 到作用域，这可能是读取器、 所有者或参与者角色。 在某些情况下，它可能显示为空白，以指示可能有权访问订阅，但不是到管理组它属于。 其他列中的信息各不相同，具体取决于你的角色。 角色是在确定哪些数据可以看到和可执行在分配策略或计划 （所有者），编辑它们，或查看符合性方面的操作的密钥。 |
| **总虚拟机** | 该范围内的 Vm 数。 为管理组，它是在订阅或子管理组下嵌套的 Vm 总数。 |
| **分配覆盖率** | 受策略或计划的 Vm 的百分比。 |
| **分配状态** | 您的策略或计划分配的状态信息。 |
| **兼容的 Vm** | 符合策略或计划下的 Vm 数量。 为该计划**启用 Vm 的 Azure Monitor**，这是具有 Log Analytics 代理和依赖关系代理的 Vm 数。 在某些情况下，它可能显示为空白由于没有赋值、 没有 Vm 或没有足够的权限。 下提供的信息**符合性状态**。 |
| **合规性** | 符合性的总数是通过的所有非重复的资源和非重复资源，将符合划分的总和。 |
| **符合性状态** | 策略或计划分配的符合性状态的信息。|

分配策略或计划后，选择分配中的作用域可能是列出该作用域或其子集。 例如，您可能已创建订阅 （策略作用域） 而不是管理组 （覆盖范围） 的工作分配。 在本示例中的值**分配覆盖率**指示策略或计划除以覆盖率作用域中的虚拟机的作用域中的 Vm。 在另一种情况下，您可能有一些虚拟机、 资源组或订阅从排除策略作用域。 如果值为空，则表示策略或计划不存在或你没有权限。 下提供的信息**分配状态**。

## <a name="enable-by-using-azure-policy"></a>通过使用 Azure Policy 启用

若要在租户中使用 Azure Policy 启用用于 VM 的 Azure Monitor，请执行以下操作：

- 将在计划分配给作用域： 管理组、 订阅或资源组。
- 查看并修正符合性结果。

有关分配 Azure Policy 的详细信息，请参阅 [Azure Policy 概述](../../governance/policy/overview.md#policy-assignment)，并在继续操作前查看[管理组概述](../../governance/management-groups/index.md)。

### <a name="policies-for-azure-vms"></a>适用于 Azure Vm 的策略

下表中列出的 Azure VM 的策略定义。

|Name |描述 |Type |
|-----|------------|-----|
|[预览]：启用用于 VM 的 Azure Monitor |为指定的作用域 （管理组、 订阅或资源组） 中的虚拟机中启用 Azure Monitor。 将 Log Analytics 工作区用作参数。 |计划 |
|[预览]：审核依赖项代理部署 – VM 映像 (OS) 未列出 |如果在列表中未定义 VM 映像 (OS)，并且未安装代理，Vm 报告为不符合要求。 |策略 |
|[预览]：Audit Log Analytics 代理部署 – VM 映像 (OS) 未列出 |如果在列表中未定义 VM 映像 (OS)，并且未安装代理，Vm 报告为不符合要求。 |策略 |
|[预览]：部署 Linux Vm 的依赖关系代理 |如果在列表中定义 VM 映像 (OS)，并且未安装代理，请部署 Linux Vm 的依赖关系代理。 |策略 |
|[预览]：部署 Windows Vm 的依赖关系代理 |如果在列表中定义 VM 映像 (OS)，并且未安装代理，请部署 Windows Vm 的依赖关系代理。 |策略 |
|[预览]：部署 Linux Vm 的 Log Analytics 代理 |部署 Linux Vm 的 Log Analytics 代理，如果在列表中定义 VM 映像 (OS)，并且未安装代理。 |策略 |
|[预览]：部署 Windows Vm 的 Log Analytics 代理 |部署 Windows Vm 的 Log Analytics 代理，如果在列表中定义 VM 映像 (OS)，并且未安装代理。 |策略 |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>策略的 Azure 虚拟机规模集

下表中列出的 Azure 虚拟机规模集的策略定义。

|名称 |描述 |Type |
|-----|------------|-----|
|[预览]：Azure Monitor 启用虚拟机规模集 |为虚拟机规模集 （管理组、 订阅或资源组） 指定的作用域中启用 Azure Monitor。 将 Log Analytics 工作区用作参数。 请注意:如果规模集升级策略设置为手动，可将扩展应用到集中的所有 Vm，通过在其上调用升级。 在 CLI 中，这是 az vmss 更新实例。 |计划 |
|[预览]：审核依赖项代理部署在虚拟机规模集-VM 映像 (OS) 未列出 |报告虚拟机规模集为不符合要求，如果在列表中未定义 VM 映像 (OS)，并且未安装代理。 |策略 |
|[预览]：Audit Log Analytics 代理部署在虚拟机规模集-VM 映像 (OS) 未列出 |报告虚拟机规模集为不符合要求，如果在列表中未定义 VM 映像 (OS)，并且未安装代理。 |策略 |
|[预览]：部署 Linux 虚拟机规模集的依赖关系代理 |部署的 Linux 虚拟机规模集如果在列表中定义 VM 映像 (OS)，并且未安装代理的依赖关系代理。 |策略 |
|[预览]：部署 Windows 虚拟机规模集的依赖关系代理 |部署的 Windows 虚拟机规模集如果在列表中定义 VM 映像 (OS)，并且未安装代理的依赖关系代理。 |策略 |
|[预览]：部署 Linux 虚拟机规模集的 Log Analytics 代理 |部署适用于 Linux 虚拟机规模集如果在列表中定义 VM 映像 (OS)，并且未安装代理的 Log Analytics 代理。 |策略 |
|[预览]：部署 Windows 虚拟机规模集的 Log Analytics 代理 |部署的 Windows 虚拟机规模集如果在列表中定义 VM 映像 (OS)，并且未安装代理的 Log Analytics 代理。 |策略 |

以下介绍独立策略（未包含在计划中）：

|名称 |描述 |Type |
|-----|------------|-----|
|[预览]：VM – 报告不匹配的 audit Log Analytics 工作区 |如果它们不会记录到指定策略或计划分配中的 Log Analytics 工作区，Vm 报告为不符合要求。 |策略 |

### <a name="assign-the-azure-monitor-initiative"></a>分配 Azure Monitor 计划
若要创建策略分配从**Vm 策略覆盖范围的 Azure Monitor**页上，执行以下步骤。 若要了解如何完成这些步骤，请参阅 [从 Azure 门户创建策略分配](../../governance/policy/assign-policy-portal.md)。

分配策略或计划后，选择分配中的作用域可能是此处列出的作用域或其子集。 例如，您可能已创建的订阅 （策略作用域） 和不是管理组 （覆盖范围） 分配。 在这种情况下，覆盖率百分比将指示的策略或计划的作用域中的虚拟机除以覆盖率作用域中的虚拟机。 在另一种情况下，您可能有一些虚拟机或资源组或订阅从排除策略作用域。 如果为空，则表示策略或计划不存在或你没有权限。 下提供的信息**分配状态**。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在 Azure 门户中选择“监视”。  

3. 在“见解”分区中选择“虚拟机(预览)”   。
 
4. 选择**开始**选项卡。在页上，选择**管理的策略覆盖范围**。

5. 从表中选择管理组或订阅。 选择**作用域**通过选择省略号 （...）。在示例中，作用域限制强制的虚拟机的分组策略的分配。

6. 上**Azure 策略分配**页上，则它已预填充计划**启用 Vm 的 Azure Monitor**。 
    **分配名称**框自动填充具有计划的名称，但您可以更改它。 您还可以添加可选的描述。 **由分配**框自动填充根据登录的用户。 此值是可选的。

7. （可选）若要从范围中删除一个或多个资源，请选择“排除项”  。

8. 在受支持区域的“Log Analytics 工作区”下拉列表中，选择一个工作区  。

   > [!NOTE]
   > 如果工作区超出分配范围，则将 *Log Analytics 参与者*权限授予策略分配的主体 ID。 如果不这样做，可能会看到如部署故障`The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`若要授予访问权限，查看[如何手动配置托管的标识](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)。
   > 
   >  **托管标识**已选中复选框，因为正在分配的计划包含的策略*deployIfNotExists*效果。
    
9. 在“管理标识位置”下拉列表中，选择适当的区域  。

10. 选择“分配”。 

创建分配的之后, **Vm 策略覆盖范围的 Azure Monitor**页上更新**分配覆盖率**，**分配状态**，**符合虚拟机**，并**符合性状态**以反映所做的更改。 

以下矩阵映射每个可能符合性状态的计划。  

| 符合性状态 | 描述 | 
|------------------|-------------|
| **符合** | 作用域中的所有 Vm 都都部署到他们的 Log Analytics 和依赖关系代理。|
| **不符合** | 作用域中的并非所有 Vm 都均存储在 Log Analytics 和依赖关系代理部署到它们，并且可能需要修正。|
| **未启动** | 添加了新的分配。 |
| **Lock** | 你没有足够的特权来管理组。<sup>1</sup> | 
| **空白** | 无策略分配。 | 

<sup>1</sup>如果没有对管理组访问权限，要求所有者提供访问权限。 或者，查看符合性和管理通过子管理组或订阅的分配。 

下表列出了每个可能的分配状态的计划。

| 分配状态 | 描述 | 
|------------------|-------------|
| **Success** | 作用域中的所有 Vm 都都部署到他们的 Log Analytics 和依赖关系代理。|
| **警告** | 订阅不是管理组下。|
| **未启动** | 添加了新的分配。 |
| **Lock** | 你没有足够的特权来管理组。<sup>1</sup> | 
| **空白** | 没有虚拟机存在，或者未分配到策略。 | 
| **Action** | 分配策略或编辑分配。 | 

<sup>1</sup>如果没有对管理组访问权限，要求所有者提供访问权限。 或者，查看符合性和管理通过子管理组或订阅的分配。

## <a name="review-and-remediate-the-compliance-results"></a>检查和修正合规结果

下面的示例适用于 Azure VM，但它也适用于虚拟机规模集。 若要了解如何查看符合性结果，请参阅[识别不符合性结果](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)。 上**Vm 策略覆盖范围的 Azure Monitor**页上，从表中选择管理组或订阅。 选择**查看符合性**通过选择省略号 （...）。   

![Azure VM 的策略合规性](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

根据计划中包含的策略的结果，Vm 将报告为不符合要求在以下方案中：

* 未部署 log Analytics 代理或依赖关系代理。  
    这对于已经拥有 VM 的范围来说是典型情况。 若要缓解此问题，部署所需的代理[创建修补任务](../../governance/policy/how-to/remediate-resources.md)的不符合策略。  
    - [预览]: Deploy Dependency agent for Linux VMs
    - [预览]: Deploy Dependency agent for Windows VMs
    - [预览]: Deploy Log Analytics agent for Linux VMs
    - [预览]: Deploy Log Analytics agent for Windows VMs

* VM 映像 (OS) 不被标识策略定义中。  
    部署策略标准仅包含通过已知 Azure VM 映像部署的 VM。 请查看文档，了解 VM OS 是否受支持。 如果不受支持，则复制部署策略并更新或修改它来使映像合规。  
    - [预览]：审核依赖项代理部署 – VM 映像 (OS) 未列出
    - [预览]：Audit Log Analytics 代理部署 – VM 映像 (OS) 未列出

* VM 未登录到指定的 Log Analytics 工作区。  
    可能存在计划范围中的某些 VM 登录到策略部署中指定的 Log Analytics 工作区以外的 Log Analytics 工作区的情况。 此策略是一个工具，以确定哪个 Vm 向不符合要求的工作区进行报告。  
    - [预览]: Audit Log Analytics workspace for VM – Report mismatch

## <a name="edit-an-initiative-assignment"></a>编辑计划分配

在任何时间后将计划分配到管理组或订阅，您可以编辑它以修改以下属性：

- 分配名称
- 描述
- 由分配
- Log Analytics 工作区
- 例外

## <a name="next-steps"></a>后续步骤

现在，你的虚拟机启用监视，此信息是可用于分析的 Vm 的 Azure monitor。 

- 若要了解如何使用运行状况的功能，请参阅[查看 Azure Vm 运行状况的监视器](vminsights-health.md)。 
- 若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。 
- 若要标识瓶颈和 VM 的性能的整体利用率，请参阅[查看 Azure VM 性能](vminsights-performance.md)。 
- 若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。
