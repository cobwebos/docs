---
title: "虚拟机规模集概述 | Microsoft Docs"
description: "了解有关虚拟机规模集的详细信息"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 1/25/2017
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: 868d65642ab9ca3e1c35d33a7cb9e9dd8c31c430
ms.openlocfilehash: f945ac0357c11c70780dea8e62f094457a213d96


---
# <a name="virtual-machine-scale-sets-overview"></a>虚拟机规模集概述
虚拟机缩放集是一种 Azure 计算资源，可用于部署和管理一组相同的 VM。 VM 规模集中的所有 VM 均采用相同的配置，专用于支持真正的自动缩放，而无需对 VM 进行预配，这可更简便地生成面向大计算、大数据、容器化工作负荷的大规模服务。

对于需要扩大和缩小计算资源的应用程序，缩放操作在容错域和更新域之间进行隐式平衡。 有关 VM 规模集的简介，请参阅 [Azure 博客公告](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/)。

有关 VM 缩放集的详细信息，请查看这些视频：

* [Mark Russinovich 谈论 Azure 规模集](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Guy Bowerman 介绍虚拟机规模集](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>创建和管理 VM 缩放集
可以在 [Azure 门户](https://portal.azure.com)中选择“新建”，然后在搜索栏中键入“缩放”，来创建 VM 规模集。 结果中会看到“虚拟机规模集”。 从这里，可以填写必填字段，自定义和部署缩放集。 请注意，在门户中还有根据 CPU 使用率设置基本的自动缩放规则的选项。

也可以使用 JSON 模板和 [REST API](https://msdn.microsoft.com/library/mt589023.aspx) 定义和部署 VM 规模集，就像定义和部署单个 Azure Resource Manager VM 一样。 因此，可以使用任何标准的 Azure 资源管理器部署方法。 有关模板的详细信息，请参阅[创作 Azure Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md)。

可以在[此处](https://github.com/Azure/azure-quickstart-templates)的 Azure 快速入门模板 GitHub 存储库中找到一组 VM 规模集示例模板。 （查找名称中包含 *vmss* 的模板）

在这些模板的详细页中包含一个按钮，使用该按钮可链接到门户部署功能。 若要部署 VM 缩放集，请单击该按钮，然后填写门户中所需的任何参数。 如果不确定某个资源是否支持大写或大小写混合，则更为安全的做法是参数值始终使用小写字母和数字。 此外，此处还对 VM 缩放集模板进行了方便的视频解剖：

[VM 规模集模板详细分析](https://channel9.msdn.com/Blogs/Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>扩大和缩小 VM 缩放集
若要增加或减少 VM 规模集中的虚拟机数目，只需更改 *capacity* 属性并重新部署模板。 这种简单性可让你在想要定义 Azure 自动缩放不支持的自定义缩放事件时轻松编写自己的自定义缩放层。

如果要重新部署模板以更改容量，则可以定义只包括“SKU”属性包和已更新容量的更小模板。 [此处](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)显示了一个示例。

若要浏览创建自动缩放的规模集的步骤，请参阅[自动缩放虚拟机规模集中的虚拟机](virtual-machine-scale-sets-windows-autoscale.md)

## <a name="monitoring-your-vm-scale-set"></a>监视 VM 缩放集
[Azure 门户](https://portal.azure.com)会列出规模集并显示基本属性和操作，包括列出该集中的 VM 和显示资源使用情况图表。 有关详细信息，可使用 [Azure 资源浏览器](https://resources.azure.com)来查看 VM 规模集。 VM 缩放集是 Microsoft.Compute 下的资源，因此，你可以通过在此站点中展开以下链接来查看它们：

**订阅 -> 你的订阅 -> resourceGroups -> 提供程序 -> Microsoft.Compute -> virtualMachineScaleSets -> 你的 VM 规模集 -> 等等**

## <a name="vm-scale-set-scenarios"></a>VM 缩放集方案
本部分列出了一些典型的 VM 缩放集方案。 一些高级 Azure 服务（如 Batch（批处理）、Service Fabric、Azure 容器服务）将使用这些方案。

* **通过 RDP/SSH 连接到 VM 规模集实例** - VM 规模集是在 VNET 中创建的，并且没有为规模集中单独的 VM 分配公共 IP 地址。 这是一件好事，因为您通常不希望承担为计算网格中的所有无状态资源分配单独的公共 IP 地址而产生的支出和管理开销，并且您可以轻松地从 VNET 中的其他资源（包括负载平衡器或独立虚拟机等具有公共 IP 地址的资源）连接到这些 VM。
* **使用 NAT 规则连接到 VM** - 可以创建一个公共 IP 地址，并将其分配给负载均衡器，然后定义入站 NAT 池，用于将 IP 地址上的端口映射到 VM 规模集中 VM 上的端口。 例如：
  
  | 源 | Source Port | 目标 | Destination Port |
  | --- | --- | --- | --- |
  |  公共 IP |端口 50000 |vmss\_0 |端口 22 |
  |  公共 IP |端口 50001 |vmss\_1 |端口 22 |
  |  公共 IP |端口 50002 |vmss\_2 |端口 22 |
  
   下面的示例创建了一个 VM 规模集，该规模集使用 NAT 规则，通过单个公共 IP 实现与规模集中每个 VM 的 SSH 连接：[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   下面的示例使用 RDP 和 Windows 实现相同的目的：[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **使用“jumpbox”连接到 VM** - 如果在同一个 VNET 中创建一个 VM 规模集和一个独立 VM，则该独立 VM 和 VM 规模集 VM 能够使用其由 VNET/子网定义的内部 IP 地址彼此连接。 如果创建一个公共 IP 地址并将其分配给独立 VM，则可以通过 RDP 或 SSH 连接到该独立 VM，然后从该虚拟机连接到 VM 缩放集实例。 此时你可能会发现，与使用其默认配置中的公共 IP 地址的简单独立 VM 相比，简单的 VM 缩放集本质上更安全。
  
   例如，以下模板将使用一个独立的 VM 部署简单的规模集：[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox)
* **负载均衡到 VM 规模集实例** - 如果想要使用“轮循机制”方法向 VM 的计算群集交付工作，则可以使用第&4; 层负载均衡规则对 Azure Load Balancer 进行相应的配置。 可以定义探测，通过使用指定的协议、间隔和请求路径对端口执行 ping 操作来验证应用程序是否正在运行。 Azure [应用程序网关](https://azure.microsoft.com/services/application-gateway/)也支持规模集，以及第&7; 层和更复杂的负载均衡方案。
  
   以下示例将创建运行 Apache Web 服务器的 VM 规模集，并使用负载均衡器来均衡每个 VM 接收的负载：[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl)（查看 Microsoft.Network/loadBalancers 资源类型以及 virtualMachineScaleSet 中的 networkProfile 和 extensionProfile）
* **在 PaaS 群集管理器中将 VM 规模集部署为计算群集** - VM 规模集有时作为下一代辅助角色进行说明。 这是有效的描述，但也可能导致将规模集功能与 PaaS v1 辅助角色功能混淆。 在某种意义上，VM 规模集提供真正的“辅助角色”或辅助角色资源，并在此资源中提供独立于平台/运行时且集成到 Azure Resource Manager IaaS 中的可自定义通用计算资源。
  
   PaaS v1 辅助角色虽然在平台/运行时支持方面受到限制（仅 Windows 平台映像），但它也包括多项服务，如 VIP 交换，可配置升级设置，以及*尚未*在 VM 规模集中提供，或者将由 Service Fabric 等其他更高级别 PaaS 服务提供的特定于运行时/应用部署的设置。 考虑到这一点，你可以将 VM 缩放集视为支持 PaaS 的基础结构。 即， 生成 Service Fabric 等 PaaS 解决方案或 Mesos 等群集管理器时，可以在将 VM 缩放集作为可缩放计算层的基础上进行生成。
  
   有关 Azure 容器服务如何使用容器协调器部署基于规模集的群集的示例，请参阅：[https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)。

## <a name="vm-scale-set-performance-and-scale-guidance"></a>VM 缩放集性能和缩放指南
* 在将规模集和非托管磁盘配合使用时，为每个存储帐户规划不超过 20 个 VM（除非将“overprovision”属性设置为"false"，这种情况下最多可规划 40 个 VM）。 使用托管磁盘时，这些单个存储帐户的限制不适用。
* 将规模集与非托管磁盘配合使用时，使存储帐户名称的第一个字母尽可能互不相同。 [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/)中的示例 VMSS 模板提供了如何执行此操作的示例。
* 将自定义 VM 与非托管磁盘配合使用时，请为单个存储帐户中的每个 VM 规模集规划不超过 40 VM。 你将需要预先将映像复制到存储帐户，然后才能开始进行 VM 缩放集部署。 使用托管磁盘的规模集最多可支持 100 个自定义映像 VM。 有关详细信息，请参阅 FAQ。
* 为每个 VNET 规划不超过 4096 个 VM。
* 您可以创建的 VM 数量受到在其中进行部署的区域中核心配额的限制。 即使你对用于云服务或 IaaS v1 的核心已具有较高的限制，也仍可能需要联系客户支持才可增加计算配额限制。 若要查询您的配额，可运行以下 Azure CLI 命令：`azure vm list-usage` 和以下 PowerShell 命令：`Get-AzureRmVMUsage`（如果使用的 PowerShell 版本低于 1.0，则使用 `Get-AzureVMUsage`）。

## <a name="vm-scale-set-frequently-asked-questions"></a>VM 缩放集常见问题
**问：** VM 缩放集中可以有多少个 VM？

**答：** 使用托管磁盘时，规模集可以有 0 到 1,000 个基于平台映像的 VM，或者 0-100 个基于自定义映像的 VM。 对于非托管磁盘（可以在其中定义自己的存储帐户），其限制为平台映像 100 个 VM，自定义映像最多 40 个 VM（如果 *overprovision* 属性设置为“false”，则默认为 20 个 VM），因为自定义映像用于非托管磁盘时，仅限使用单个存储帐户。

**问：** VM 缩放集内是否支持数据磁盘？

**答：** 是的。 使用托管存储定义的规模集可以定义附加数据驱动器配置，该配置将适用于集中所有 VM。 不使用托管存储定义的规模集没有附加数据驱动器。 可用于存储数据的其他选项包括：

* Azure 文件（SMB 共享驱动器）
* OS 驱动器
* 临时驱动器（本地，不受 Azure 存储的支持）
* Azure 数据服务（如 Azure 表、Azure blob）
* 外部数据服务（如远程 DB）

**问：** 哪些 Azure 区域支持 VM 规模集？

**答：** 所有区域都支持 VM 规模集。

**问：** 如何使用自定义映像创建 VM 规模集？

**答：** 将 vhdContainers 属性留空（或将其省略）并提供映像属性的 URI。 例如：[201-vmss-windows-customimage].(https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-customimage)


**问：** 如果我将 VM 缩放集容量从 20 减少到 15，将删除哪些 VM？

**答：** 将从跨升级域和容错域的缩放集中均匀地删除虚拟机，以最大限度地提高可用性。 将首先删除具有最高 ID 的 VM。

**问：** 如果之后我再将容量从 15 增加到 18，又将发生什么情况？

**答：** 如果将容量增加到 18，则将创建 3 个新 VM。 每次 VM 实例 ID 将从以前的最高值（如 20、21、22）进行递增。 FD 与 UD 中的 VM 都是平衡的。

**问：** 在一个 VM 缩放集中使用多个扩展时，是否可以强制规定一个执行序列？

**答：** 不能直接强制执行，但对于 customScript 扩展，脚本可以等待另一个扩展来完成操作（[例如通过监视扩展日志](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)）。 在 [Extension Sequencing in Azure VM Scale Sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)（Azure VM 规模集中的扩展序列）这篇博客文章中可以找到有关扩展序列的其他指导。

**问：** VM 缩放集是否可与 Azure 可用性集配合使用？

**答：** 是的。 VM 规模集是含有 5 个 FD 和 5 个 UD 的隐式可用性集。 无需在 virtualMachineProfile 下进行任何配置。 VM 规模集如果包含 100 个以上的 VM，则会跨多个“位置组”，等效于多个可用性集。 由 VM 组成的可用性集可以与由 VM 组成的规模集位于相同的 VNET 中。 常见的配置是将控制节点 VM（通常需要可用性集中的配置）和数据节点置于规模集中。



<!--HONumber=Feb17_HO1-->


