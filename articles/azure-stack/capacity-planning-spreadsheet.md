---
title: 容量规划 spreadheet 适用于 Azure Stack |Microsoft Docs
description: 了解有关容量规划电子表格进行 Azure Stack 部署。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.openlocfilehash: ec4d8ef43510c07e73ab18de227176d3c282b8e1
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740835"
---
# <a name="azure-stack-capacity-planner"></a>Azure Stack 容量计划程序
Azure Stack 容量规划器是用于 Azure Stack 资源容量规划电子表格。 容量规划器为您提供的功能设计各种分配的计算资源，并查看如何跨多个选中的硬件产品/服务适合这些。 下面提供了详细介绍了如何使用 Azure Stack 计算器。

## <a name="worksheet-descriptions"></a>工作表说明
下面是简要总结了可以从下载的 Azure Stack 容量规划器电子表格中包含的工作表[ http://aka.ms/azstackcapacityplanner ](http://aka.ms/azstackcapacityplanner):

|选项卡名称|说明|
|-----|-----|
|版本免责声明|计算器、 版本号和发行的日期的用途的简要概述。|
|说明|提供有关使用 Azure Stack 容量规划器的详细的说明。|
|DefinedSolutionSKUs|多列包含最多五个硬件定义的表。 此表中的条目是示例。 其目的是，用户将会更改详细信息，以匹配正在考虑针对使用的系统配置或购买。|
|DefineByVMFootprint|通过创建各种大小和数量的虚拟机的集合中查找相应的硬件 SKU。|
|DefineByWorkloadFootprint|通过创建一个 Azure Stack 工作负载的集合中查找相应的硬件 SKU。|
|  |  |

## <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs 说明
此工作表包含最多五个硬件定义示例。 更改详细信息，以匹配正在考虑针对使用的系统配置或购买。

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>由授权的硬件合作伙伴提供的硬件选择
Azure Stack 作为集成的系统附带了安装解决方案合作伙伴的软件。 这些解决方案合作伙伴将拥有容量规划工具以及这些工具应使用它来完成的解决方案容量讨论的 Azure Stack 自身，权威版本。

### <a name="multiple-ways-to-model-computing-resources"></a>通过多种方式来计算资源的模型
使用资源内的 Azure Stack 规划器建模的基本构建基块是 Azure Stack 虚拟机 (Vm) 的各种大小。 这些 Vm 范围的大小从最小，"基本 0"，最多的当前最大的 VM"Standard_Fsv2。" 各种不同的 Vm 数量根据你的需求，用于创建两个不同的方式使用此工具的计算资源分配。

1. 规划器用户选择特定的硬件产品/服务，然后会看到各种资源的组合适合此硬件资源。 

2. 规划器用户创建的 VM 分配的特定组合，然后可以让 Azure 资源计算器显示的可用硬件 Sku 能够支持该 VM 配置。

此工具提供了两种方法来分配 VM 资源;或 VM 的资源分配的一个单个集合作为最多六个不同的工作负荷配置的集合。 每个工作负荷配置可以包含不同的分配的可用 VM 资源。 下面提供了用于创建和使用每一种分配模式的详细的信息。 非背景中包含的唯一值以阴影显示单元格，或在此工作表上的 SKU 的下拉列表中应进行修改。 阴影的单元格内所做的更改可能会破坏资源的计算。


## <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint 说明
若要创建使用各种大小和数量的 Vm 的单个集合的模型，请选择"DefineByVMFootprint"选项卡并执行这一序列的步骤。

1. 在此工作表的右上角，使用提供的下拉列表框控件以选择一个初始数量的服务器 （4 到 16 个，） 您想要安装在每个硬件系统 (SKU)。 此数量的服务器可能会修改在建模过程来查看它如何影响总体期间随时可用资源以了解你的资源分配模型。
2. 如果你想要针对一个特定的硬件配置的各种 VM 资源分配的模型，页面的右上角找到"当前 SKU"标签的正下方的蓝色下拉列表框。 提取此列表框，然后选择你所需的硬件 SKU。
3. 现在您就可以开始将各种大小的 Vm 添加到您的模型。 若要包含特定的 VM 类型，数量值的蓝色空心框中输入到该 VM 条目的左侧。

  > [!NOTE]
  > 每个 VM 开始最初分配的存储大小。 存储大小使用列表框中显示，并且可以进行修改以适合每个 Azure Stack VM 所需的存储资源级别。 如果未提供你想要使用的存储大小，可以将其添加通过修改任何包含页右侧找到"可用的存储配置"列表中 10 个初始大小。<br><br>每个 VM 开始最初分配的本地临时存储。 以反映临时存储精简预配本地 temp 数量可以更改为包括允许的最大临时存储量的下拉菜单中的任何内容。

4. 添加 Vm 时，你将看到的图表，显示可用的 SKU 资源更改。 这样，您可以查看在建模过程中添加各种大小和数量的 Vm 的效果。 若要查看更改效果的另一种方法是观看可用 Vm 列表的正下方列出的已使用且仍可用数目。 这些数字反映估计的值基于当前所选硬件 SKU。
5. 创建您的 Vm 集后，您可以通过单击正下方的"当前 SKU"标签页右上角的"建议的 SKU"按钮查找推荐的硬件 SKU。 使用此按钮，你可以随后修改 VM 配置并查看特定的硬件支持的每个配置。


## <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint 说明
若要创建使用 Azure Stack 工作负载的集合的模型，请选择"DefineByWorkloadFootprint"选项卡并执行这一序列的步骤。 使用可用的 VM 资源创建 azure Stack 工作负荷。   

> [!TIP]
> 若要更改 Azure Stack VM 提供的存储大小，请参阅步骤 3 中在上一部分中的说明。

1. 在此页的右上角，使用提供的下拉列表框控件以选择一个初始数量的服务器 （4 到 16 个，） 您想要安装在每个硬件系统 (SKU)。
2. 如果你想要针对一个特定的硬件配置的各种 VM 资源分配的模型，页面的右上角找到"当前 SKU"标签的正下方的蓝色下拉列表框。 提取此列表框，然后选择你所需的硬件 SKU。
3. 上文所述 DefineByVMFootprint 说明的步骤 3 中为每个所需 Azure Stack Vm DefineByVMFootprint 页上选择适当的存储大小。 每个 VM 的存储大小是 DefineByVMFootprint 表中定义的。
4. 从 DefineByWorkloadFootprint 页的左上角开始，通过输入该工作负荷中包含每个 VM 类型的数量来创建配置最多六个不同的工作负荷类型。 这是将数值放入该工作负荷的名称的正下方的列。 工作负荷名称可能会修改以反映此特定的配置将支持的工作负荷的类型。
5. 通过输入值在该列正下方的"Quantity"标签的底部，可能包括特定数量的每个工作负荷类型。
6. 创建工作负荷类型和数量覆盖范围后, 单击"建议的 SKU"页上，"当前 SKU"标签的正下方的右上角中找到的按钮将导致最小 SKU 具有足够的资源来支持此总体若要显示的工作负荷的配置。
7. 进一步建模可能： 通过修改硬件 SKU，所选服务器的数目或更改的 VM 分配或工作负荷配置中的数量。 关联的关系图将显示显示所做的更改如何影响总体资源消耗的即时反馈。
8. 后所做的更改满意后，再次单击"建议的 SKU"按钮会显示建议的新配置的 SKU。


## <a name="next-steps"></a>后续步骤
了解有关[适用于 Azure Stack 数据中心集成注意事项](azure-stack-datacenter-integration.md)
