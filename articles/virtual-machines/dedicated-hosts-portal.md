---
title: 使用 Azure 门户部署 Azure 专用主机
description: 使用 Azure 门户将 Vm 和规模集部署到专用主机。
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/04/2020
ms.author: cynthn
ms.openlocfilehash: a6bef4944207e26f2de93daa89fa1418c5c44c4f
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91373080"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts-using-the-portal"></a>使用门户将 Vm 和规模集部署到专用主机 

本文介绍了如何创建 Azure [专用主机](dedicated-hosts.md)来托管虚拟机 (VM)。 


> [!IMPORTANT]
> 本文还介绍了自动放置 Vm 和规模集实例。 自动放置当前为公共预览版。
> 若要参与预览，请完成中的预览加入调查 [https://aka.ms/vmss-adh-preview](https://aka.ms/vmss-adh-preview) 。
> 若要在 Azure 门户中浏览预览功能，必须使用此 URL： [https://aka.ms/vmssadh](https://aka.ms/vmssadh) 。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="limitations"></a>限制

- 专用主机可用的大小和硬件类型因区域而异。 请参阅主机[定价页](https://aka.ms/ADHPricing)来了解详细信息。

## <a name="create-a-host-group"></a>创建主机组

主机组是表示专用主机集合的资源。 可在区域和可用性区域中创建主机组，并向其添加主机。 规划高可用性时，有其他选项可供选择。 你可以将以下一个或两个选项与专用主机一起使用： 
- 跨多个可用性区域。 在这种情况下，你需要在要使用的每个区域中都有一个主机组。
- 跨映射到物理机架的多个容错域。 
 
在任一情况下，都需要为主机组提供容错域计数。 如果你不希望跨组中的容错域，请使用容错域计数 1。 

还可以决定使用可用性区域和容错域。 

在此示例中，我们将使用1个可用性区域和2个容错域创建一个主机组。 


1. 打开 [Azure 门户](https://portal.azure.com)。 若要尝试 **自动放置**预览，请使用以下 URL： [https://aka.ms/vmssadh](https://aka.ms/vmssadh) 。
1. 选择左上角的“创建资源”。
1. 搜索“主机组”，然后从结果中选择“主机组”。
1. 在“主机组”页中，选择“创建”。
1. 选择要使用的订阅，然后选择“新建”以创建新的资源组。
1. 键入“myDedicatedHostsRG”作为“名称”，然后选择“确定”。
1. 对于“主机组名称”，请键入“myHostGroup”。
1. 对于“位置”，请选择“美国东部”。
1. 对于 " **可用性区域**"，选择 " **1**"。
1. 选择 **2** 作为“容错域计数”。
1. 如果使用了 **自动放置** URL，请选择此选项以自动将 vm 和规模集实例分配到此组中的可用主机。
1. 选择“查看 + 创建”，然后等待验证。
1. 看到“验证通过”消息后，选择“创建”以创建主机组。 

应当只需很短时间便可创建主机组。


## <a name="create-a-dedicated-host"></a>创建专用主机

现在，在主机组中创建一个专用主机。 除了主机名称外，还需要提供主机的 SKU。 主机 SKU 捕获受支持的 VM 系列以及专用主机的硬件代系。

有关主机 SKU 和定价的详细信息，请参阅 [Azure 专用主机定价](https://aka.ms/ADHPricing)。

如果为主机组设置了容错域计数，则系统会要求你为主机指定容错域。  

1. 选择左上角的“创建资源”。
1. 搜索“专用主机”，然后从结果中选择“专用主机”。
1. 在“专用主机”页中，选择“创建”。
1. 选择要使用的订阅。
1. 选择“myDedicatedHostsRG”作为“资源组”。
1. 在 " **实例详细信息**" 中，键入 *myhost 代表* 作为 **名称** ，然后选择 " *美国东部* " 作为位置。
1. 在“硬件配置文件”中，对于“大小系列”，请选择“标准 Es3 系列 - 类型 1”；对于“主机组”，请选择“myHostGroup”；对于“容错域”，请选择 1。 至于其余字段，请保留默认值。
1. 完成后，选择“查看 + 创建”，然后等待验证。
1. 看到“验证通过”消息后，选择“创建”以创建主机。 

## <a name="create-a-vm"></a>创建 VM

1. 在 Azure 门户的左上角，选择“创建资源”。
1. 在 Azure Marketplace 资源列表上方的搜索框中，搜索并选择要使用的映像，然后选择 " **创建**"。
1. 在“基本信息”选项卡中的“项目详细信息”下，确保选择了正确的订阅，然后选择 myDedicatedHostsRG 作为“资源组” 。 
1. 在“实例详细信息”**** 下，对于“虚拟机名称”**** 键入 *myVM*，对于“位置”**** 选择“美国东部”**。
1. 在“可用性选项”中，选择“可用性区域”，然后从下拉列表中选择 1 。
1. 对于大小，选择“更改大小”。 在可用大小列表中，选择 Esv3 系列其中一个，例如“标准 E2s v3”。 可能需要清除筛选器才能查看所有可用大小。
1. 根据需要填写 " **基本** 信息" 选项卡上的其余字段。
1. 在页面顶部，选择“高级”选项卡，然后在“主机”部分，对于“主机组”，选择 myHostGroup，对于“主机”，选择 myHost 。 
    ![选择主机组和主机](./media/dedicated-hosts-portal/advanced.png)
1. 保留剩余的默认值，然后选择页面底部的“查看 + 创建”按钮。
1. 显示验证通过的消息时，选择“创建”。

部署 VM 需要数分钟。

## <a name="create-a-scale-set-preview"></a> (预览创建规模集) 

> [!IMPORTANT]
> 专用主机上的虚拟机规模集目前为公共预览版。
>
> 若要参与预览，请完成中的预览加入调查 [https://aka.ms/vmss-adh-preview](https://aka.ms/vmss-adh-preview) 。
>
> 若要在 Azure 门户中浏览预览功能，必须使用此 URL： [https://aka.ms/vmssadh](https://aka.ms/vmssadh) 。
>
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
>
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

部署规模集时，需要指定主机组。

1. 搜索 " *规模集* "，然后从列表中选择 " **虚拟机规模集** "。
1. 选择 " **添加** " 以创建新的规模集。
1. 像往常一样完成 " **基本** 信息" 选项卡上的字段，但请确保选择的 VM 大小来自你为专用主机选择的系列，如 **标准 E2s v3**。
1. 在 "**高级**" 选项卡上，选择 "**最大分布**" 作为**分配算法**。
1. 在 " **主机组**" 中，从下拉菜单中选择主机组。 如果最近创建了组，可能需要花费一分钟时间才能将其添加到列表中。

## <a name="add-an-existing-vm"></a>添加现有 VM 

可将现有 VM 添加到专用主机，但必须先停止/解除分配该 VM。 在将 VM 移动到专用主机之前，请确保 VM 配置受支持：

- VM 大小必须属于专用主机所用的同一大小系列。 例如，如果专用主机是 DSv3，则 VM 大小可以是 Standard_D4s_v3，但不能是 Standard_A4_v2。 
- VM 需要位于专用主机所在的同一区域。
- VM 不能是邻近放置组的一部分。 在将 VM 移动到专用主机之前，请先从邻近放置组中删除该 VM。 有关详细信息，请参阅[将 VM 移出邻近放置组](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- VM 不能位于可用性集中。
- 如果 VM 位于可用性区域中，则该可用性区域必须与主机组相同。 VM 和主机组的可用性区域设置必须匹配。

使用[门户](https://portal.azure.com)将 VM 迁移到专用主机。

1. 打开 VM 所对应的页。
1. 选择“停止”以停止/解除分配 VM。
1. 在左侧菜单中选择“配置”。
1. 从下拉菜单中选择主机组和主机。
1. 完成操作后，在页面顶部选择“保存”。
1. 将 VM 添加到主机之后，从左侧菜单中选择“概述”。
1. 在页面顶部，选择“启动”以重启 VM。

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅[专用主机](dedicated-hosts.md)概述。

- [此处](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有一个示例模板，该模板使用区域和容错域来最大限度地提高在某个地区的复原能力。

- 你还可以使用 [Azure CLI](./linux/dedicated-hosts-cli.md) 或 [PowerShell](./windows/dedicated-hosts-powershell.md)部署专用主机。
