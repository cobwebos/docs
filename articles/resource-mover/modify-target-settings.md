---
title: 通过 Azure 资源移动器在区域之间移动 Azure Vm 时修改目标设置
description: 了解如何在 Azure 资源移动器之间移动 Azure Vm 时修改目标设置。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: c58fdd38c4221c03778b2c769620bf7911aaded8
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89670291"
---
# <a name="modify-target-settings"></a>修改目标设置

本文介绍如何在 Azure 区域与 [Azure 资源移动器](overview.md)之间移动资源时修改目标设置。


## <a name="modify-vm-settings"></a>修改 VM 设置

移动 Azure Vm 和关联的资源时，可以修改目标设置。 

- 建议你仅在移动集合验证后更改目标设置。
- 建议你在准备资源之前修改设置，因为在准备完成后某些目标属性可能不可用于编辑。

但是：
- 如果要移动源资源，则通常可以在启动启动移动过程之前修改目标设置。
- 如果在源区域中分配了现有资源，则可以修改目标设置，直到移动提交完成。

### <a name="settings-you-can-modify"></a>您可以修改的设置

表中汇总了你可以修改的配置设置。

**资源** | **选项** 
--- | --- | --- 
**VM 名称** | 选项：<br/><br/> -在目标区域中创建一个具有相同名称的新 VM。<br/><br/> -在目标区域中创建一个具有不同名称的新 VM。<br/><br/> -使用目标区域中的现有 VM。<br/><br/> 如果你创建新的 VM，但你修改的设置例外，则会为新目标 VM 分配与源相同的设置。
**VM 可用性区域** | 将在其中放置目标 VM 的可用性区域。 如果你不想更改源设置，或者不想将 VM 放在可用性区域中，则可以将此项标记为 **NA** 。
**VM SKU** | [VM 类型](https://azure.microsoft.com/pricing/details/virtual-machines/series/) (在将用于目标 VM) 的目标区域中可用。<br/><br/> 所选目标 VM 不应小于源 VM。
**网络资源** | 虚拟网络选项 (Vnet) /network 安全组/网络接口：<br/><br/> -在目标区域中创建同名的新资源。<br/><br/> -在目标区域中创建一个具有不同名称的新资源。<br/><br/> -使用目标区域中的现有网络资源。<br/><br/> 如果创建新的目标资源，但修改的设置除外，则会为其分配与源资源相同的设置。
**公共 IP 地址名称** | 指定名称。
**公用 IP 地址 SKU** | 指定 [SKU](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku)。
**公共 IP 地址区域** | 指定标准公共 IP 地址的 [区域](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#standard) 。<br/><br/> 如果希望它是区域冗余的，请输入 as **地区性**。
**负载均衡器名称** | 指定名称。
**负载平衡器 SKU** | 基本或标准。 建议使用 Standard。
**负载均衡器区域** | 指定负载平衡器的区域。 <br/><br/> 如果希望它是区域冗余的，请输入 as **地区性**。
**资源依赖关系** | 每个依赖项的选项：<br/><br/>-资源使用将移动到目标区域的依赖源资源。<br/><br/> -资源使用位于目标区域中的不同从属资源。 在这种情况下，可以从目标区域中的任何类似资源中进行选择。

### <a name="edit-vm-target-settings"></a>编辑 VM 目标设置

如果不想将源区域中的资源依赖于目标，可以使用以下几种方法：

- 在目标区域中创建新资源。 除非指定不同的设置，否则新资源将具有与源资源相同的设置。
- 使用目标区域中的现有资源。

确切的行为取决于资源类型。 [了解](modify-target-settings.md) 有关修改目标设置的详细信息。

使用资源移动集合中的 **目标配置** 条目修改资源的目标设置。 

修改设置： 

1. 在 " **跨区域** " 页 > **目标配置** "列中，单击资源条目的链接。
2. 在 " **配置设置**" 中，可以在目标区域中创建新的 VM。
3. 向目标 VM 分配新的可用性区域、可用性集或 SKU。 **可用性区域** 和 **SKU**。

仅对正在编辑的资源进行更改。 需要单独更新任何依赖资源。


## <a name="modify-sql-settings"></a>修改 SQL 设置

移动 Azure SQL 数据库资源时，可修改移动的目标设置。 

- 对于 SQL 数据库：
    - 建议你在准备迁移之前修改目标配置设置。
    - 您可以修改目标数据库的设置和数据库的区域冗余。
- 对于弹性池：
    -  可以在开始移动前随时修改目标配置。
    - 您可以修改目标弹性池和池的区域冗余。 

### <a name="sql-settings-you-can-modify"></a>可修改的 SQL 设置

**设置** | **SQL 数据库** | **弹性池**
--- | --- | ---
**名称** | 在目标区域中创建一个具有相同名称的新数据库。<br/><br/> 在目标区域中创建一个具有不同名称的新数据库。<br/><br/> 使用目标区域中的现有数据库。 | 在目标区域中创建一个具有相同名称的新弹性池。<br/><br/> 在目标区域中创建一个具有不同名称的新弹性池。<br/><br/> 在目标区域中使用现有的弹性池。
**区域冗余** | 若要从支持区域冗余的区域移动到不支持的区域，请在区域设置中键入 **Disable** 。<br/><br/> 若要从不支持区域冗余的区域移动到执行的区域，请在区域设置中键入 **Enable** 。 | 若要从支持区域冗余的区域移动到不支持的区域，请在区域设置中键入 **Disable** 。<br/><br/> 若要从不支持区域冗余的区域移动到执行的区域，请在区域设置中键入 **Enable** 。

### <a name="edit-sql-target-settings"></a>编辑 SQL 目标设置

按如下所示修改 Azure SQL 数据库资源的目标设置： 

1. 在 **跨区域**，对于要修改的资源，单击 "目标" **配置** 条目。
2. 在 " **配置设置**" 中，指定上表中汇总的目标设置。

## <a name="next-steps"></a>后续步骤

[将 AZURE VM 移](tutorial-move-region-virtual-machines.md) 到另一个区域。
