---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 262880997c6b065dc5293a18d9a07c52ac836f37
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703029"
---
> [!IMPORTANT]
> Azure 专用主机目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> **已知预览版限制**
> - 虚拟机规模集目前在专用主机上不受支持。
> - 预览版初始版本支持以下 VM 系列:DSv3 和 ESv3。 


## <a name="create-a-host-group"></a>创建主机组

**主机组**是表示一组专用主机的新资源。 在区域和可用性区域中创建主机组, 并向其添加主机。 规划高可用性时, 有其他选项可供选择。 你可以将以下一个或两个选项与专用主机一起使用: 
- 跨多个可用性区域。 在这种情况下, 你需要在要使用的每个区域中都有一个主机组。
- 跨多个容错域, 这些容错域映射到物理机架。 
 
在任一情况下, 都需要提供主机组的容错域计数。 如果你不希望跨组中的容错域, 请使用容错域计数1。 

您还可以决定使用可用性区域和容错域。 

在此示例中, 我们将使用1个可用性区域和2个容错域创建一个主机组。 


1. 打开 Azure[门户](https://portal.azure.com)。
1. 选择左上角的 "**创建资源**"。
1. 搜索 "**主机组**", 然后从结果中选择 "**主机组 (预览版)** "。

    ![主机组搜索结果。](./media/virtual-machines-common-dedicated-hosts-portal/host-group.png)
1. 在 "**主机组 (预览)** " 页中, 选择 "**创建**"。
1. 选择要使用的订阅, 然后选择 "**新建**" 以创建新的资源组。
1. 键入*myDedicatedHostsRG*作为**名称**, 然后选择 **"确定"** 。
1. 对于 "**主机组名称**", 请键入*myHostGroup*。
1. 对于 "**位置**", 请选择 "**美国东部**"。
1. 对于 "**可用性区域**", 选择 " **1**"。
1. 对于 "**容错域计数**", 请选择**2**。
1. 选择 "**查看 + 创建**", 然后等待验证。

    ![主机组设置](./media/virtual-machines-common-dedicated-hosts-portal/host-group-settings.png)
1. 看到 "**验证通过**" 消息后, 选择 "**创建**" 以创建主机组。

只需几分钟即可创建主机组。

## <a name="create-a-dedicated-host"></a>创建专用主机

现在, 在主机组中创建专用主机。 除了主机名称外, 还需要提供主机的 SKU。 主机 SKU 捕获受支持的 VM 系列以及专用主机的硬件生成。  在预览期间, 我们将支持以下主机 SKU 值:DSv3_Type1 和 ESv3_Type1。

有关主机 Sku 和定价的详细信息, 请参阅[Azure 专用主机定价](https://aka.ms/ADHPricing)。

如果为主机组设置了容错域计数, 系统会要求你为主机指定容错域。  

1. 选择左上角的 "**创建资源**"。
1. 搜索 "**专用主机**", 然后从结果中选择 "**专用主机 (预览)** "。

    ![主机组搜索结果。](./media/virtual-machines-common-dedicated-hosts-portal/host.png)
1. 在 "**专用主机 (预览)** " 页中, 选择 "**创建**"。
1. 选择要使用的订阅。
1. 选择*myDedicatedHostsRG*作为**资源组**。
1. 在 "**实例详细信息**" 中, 键入*myhost 代表*作为**名称**, 然后选择 "*美国东部*" 作为位置。
1. 在 "**硬件配置文件**" 中, 选择 "*标准 Es3* " "系列" "系列", 为 "**主机组**" 选择 " *myHostGrup* **",** 然后选择 " *1* " 作为 "**容错域**"。 保留其余字段的默认值。
1. 完成后, 请选择 "**查看 + 创建**并等待验证"。

    ![主机设置](./media/virtual-machines-common-dedicated-hosts-portal/host-settings.png)
1. 看到 "**验证通过**" 消息后, 选择 "**创建**" 以创建主机。


