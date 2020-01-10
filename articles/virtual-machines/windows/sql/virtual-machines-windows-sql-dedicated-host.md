---
title: 在 Azure 专用主机上 SQL Server VM
description: 了解有关在 Azure 专用主机上运行 SQL Server VM 的详细信息。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: edb2d3fa670475d9b08fe05494035949181a9240
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834355"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>在 Azure 专用主机上 SQL Server VM 

本文详细介绍了如何将 SQL Server VM 与[Azure 专用主机](/azure/virtual-machines/windows/dedicated-hosts)一起使用。 有关 azure 专用主机的其他信息，请参阅博客文章[Azure 专用主机简介](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/)。 

## <a name="overview"></a>概述
[Azure 专用主机](/azure/virtual-machines/windows/dedicated-hosts)是一项服务，可提供物理服务器，这些服务器可以承载一个或多个虚拟机-专用于一个 Azure 订阅。 专用主机是在 Microsoft 数据中心中使用的、作为资源提供的物理服务器。 你可以在区域、可用性区域和容错域中预配专用主机。 然后，你可以将 Vm 直接置于预配的主机中，这种配置最适合你的需求。

## <a name="limitations"></a>限制

- 虚拟机规模集目前在专用主机上不受支持。
- 支持以下 VM 系列： DSv3 和 ESv3。 

## <a name="licensing"></a>许可

将 SQL Server VM 添加到 Azure 专用主机时，可以在两个不同的许可选项之间进行选择。 

  - **SQL VM 许可**：这是现有的许可选项，你可以在其中单独支付每个 SQL Server VM 许可证。 
  - **专用主机许可**：适用于 Azure 专用主机的新许可模型，其中 SQL Server 许可证，并在主机级别为其付费。 


用于使用现有 SQL Server 许可证的主机级选项： 
  - SQL Server Enterprise 版 Azure 混合权益
    - 可供具有 SA 或订阅的客户使用。
    - 许可所有可用的物理内核并享受无限虚拟化（最多可支持主机的最大个 vcpu）。
        - 有关将 Azure 混合权益应用到 Azure 专用主机的详细信息，请参阅[AZURE 混合权益常见问题](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)。 
  - SQL Server 在10月1日之前获取的许可证
      - SQL Server Enterprise 版同时提供了主机级和 VM 许可证选项。 
      - SQL Server Standard edition 仅具有可用的 VM 许可证选项。 
          - 有关详细信息，请参阅[Microsoft 的产品条款](https://www.microsoft.com/licensing/product-licensing/products)。 
  - 如果未选择 SQL Server 专用主机级别的选项，则可以在单个 Vm 级别选择 SQL Server AHB，就像在多租户 Vm 中一样。



## <a name="provisioning"></a>提供  
将 SQL Server VM 预配到专用主机与其他任何 Azure 虚拟机没有什么不同。 为此，可以使用[Azure PowerShell](../dedicated-hosts-powershell.md)、 [Azure 门户](../dedicated-hosts-portal.md)和[Azure CLI](../../linux/dedicated-hosts-cli.md)。

将现有 SQL Server VM 添加到专用主机的过程需要停机，但不会影响数据，并且不会丢失数据。 尽管如此，所有数据库（包括系统数据库）都应在移动之前进行备份。

## <a name="virtualization"></a>虚拟化 

专用主机的一个优点是不受限制的虚拟化。 例如，你可以拥有 64 Vcore 的许可证，但你可以将主机配置为具有 128 Vcore，因此你可以获得双重 Vcore，但只需为一半 SQL Server 许可证付费。 

由于它是你的主机，因此你有资格将虚拟化设置为1:2 比。 

## <a name="faq"></a>常见问题

**问： Azure 混合权益如何在 Azure 专用主机上使用 Windows Server/SQL Server 许可证？**

答：客户可以使用其现有 Windows Server 的值并 SQL Server 具有软件保障的许可证，也可以通过使用 Azure 混合权益来确定订阅许可证的资格。 Windows Server Datacenter 和 SQL Server Enterprise Edition 客户获取不受限制的虚拟化（在主机上尽可能多地部署 Windows Server 虚拟机，受限于底层服务器的物理容量）并使用 Azure 混合权益。  Azure 专用主机中的所有 Windows Server 和 SQL Server 工作负荷也有资格用于 Windows Server 的扩展安全更新和 SQL Server 2008/R2，无额外收费。 

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常见问题](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上 SQL Server 的定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上 SQL Server 的发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)


