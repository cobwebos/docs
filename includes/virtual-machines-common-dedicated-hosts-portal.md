---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 2d440db3e55638fcf2e3d3f3fe76f82ac4d25948
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192679"
---
## <a name="limitations"></a>限制

- 虚拟机规模集目前在专用主机上不受支持。
- 初始版本支持以下 VM 系列： DSv3、ESv3 和 Fsv2。 

## <a name="create-a-host-group"></a>创建主机组

**主机组**是表示一组专用主机的新资源。 在区域和可用性区域中创建主机组，并向其添加主机。 规划高可用性时，有其他选项可供选择。 你可以将以下一个或两个选项与专用主机一起使用： 
- 跨多个可用性区域。 在这种情况下，你需要在要使用的每个区域中都有一个主机组。
- 跨多个容错域，这些容错域映射到物理机架。 
 
在任一情况下，都需要提供主机组的容错域计数。 如果你不希望跨组中的容错域，请使用容错域计数1。 

您还可以决定使用可用性区域和容错域。 

在此示例中，我们将使用1个可用性区域和2个容错域创建一个主机组。 


1. 打开 Azure[门户](https://portal.azure.com)。
1. 选择左上角的 "**创建资源**"。
1. 搜索 "**主机组**"，然后从结果中选择 "**主机组**"。

    ![主机组搜索结果。](./media/virtual-machines-common-dedicated-hosts-portal/host-group.png)
1. 在 "**主机组**" 页上，选择 "**创建**"。
1. 选择要使用的订阅，然后选择 "**新建**" 以创建新的资源组。
1. 键入*myDedicatedHostsRG*作为**名称**，然后选择 **"确定"** 。
1. 对于 "**主机组名称**"，请键入*myHostGroup*。
1. 对于“位置”，请选择“美国东部”。
1. 对于 "**可用性区域**"，选择 " **1**"。
1. 对于 "**容错域计数**"，请选择**2**。
1. 选择 "**查看 + 创建**"，然后等待验证。

    ![主机组设置](./media/virtual-machines-common-dedicated-hosts-portal/host-group-settings.png)
1. 看到 "**验证通过**" 消息后，选择 "**创建**" 以创建主机组。

只需几分钟即可创建主机组。

## <a name="create-a-dedicated-host"></a>创建专用主机

现在，在主机组中创建专用主机。 除了主机名称外，还需要提供主机的 SKU。 主机 SKU 捕获受支持的 VM 系列以及专用主机的硬件生成。

有关主机 Sku 和定价的详细信息，请参阅[Azure 专用主机定价](https://aka.ms/ADHPricing)。

如果为主机组设置了容错域计数，系统会要求你为主机指定容错域。  

1. 选择左上角的 "**创建资源**"。
1. 搜索 "**专用主机**"，然后从结果中选择 "**专用主机**"。

    ![主机组搜索结果。](./media/virtual-machines-common-dedicated-hosts-portal/host.png)
1. 在 "**专用主机**" 页上，选择 "**创建**"。
1. 选择要使用的订阅。
1. 选择*myDedicatedHostsRG*作为**资源组**。
1. 在 "**实例详细信息**" 中，键入*myhost 代表*作为**名称**，然后选择 "*美国东部*" 作为位置。
1. 在 "**硬件配置文件**" 中，选择 "*标准 Es3* " "系列" "系列"，为 "**主机组**" 选择 " *myHostGrup* **"，** 然后选择 " *1* " 作为 "**容错域**"。 保留其余字段的默认值。
1. 完成后，请选择 "**查看 + 创建**并等待验证"。

    ![主机设置](./media/virtual-machines-common-dedicated-hosts-portal/host-settings.png)
1. 看到 "**验证通过**" 消息后，选择 "**创建**" 以创建主机。


