---
title: Azure 专用主机上的 SQL 服务器 VM
description: 了解在 Azure 专用主机上运行 SQL Server VM 的详细信息。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834355"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>Azure 专用主机上的 SQL 服务器 VM 

本文详细介绍了将 SQL Server VM 与 Azure[专用主机](/azure/virtual-machines/windows/dedicated-hosts)一起使用的细节。 有关 Azure 专用主机的其他信息，请参阅[博客文章介绍 Azure 专用主机](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/)。 

## <a name="overview"></a>概述
[Azure 专用主机](/azure/virtual-machines/windows/dedicated-hosts)是一种服务，它提供物理服务器（能够承载一个或多个虚拟机）专用于一个 Azure 订阅。 专用主机是 Microsoft 数据中心中使用的物理服务器，作为资源提供。 您可以在区域、可用性区域和容错域中预配专用主机。 然后，您可以将 VM 直接放入预配主机中，无论配置最适合您的需要。

## <a name="limitations"></a>限制

- 专用主机当前不支持虚拟机缩放集。
- 支持以下 VM 系列：DSv3 和 ESv3。 

## <a name="licensing"></a>授权

将 SQL Server VM 添加到 Azure 专用主机时，可以选择两种不同的许可选项。 

  - **SQL VM 许可**：这是现有的许可选项，您可以单独为每个 SQL Server VM 许可证付费。 
  - **专用主机许可**：适用于 Azure 专用主机的新许可模型，其中 SQL Server 许可证在主机级别捆绑并支付。 


用于使用现有 SQL Server 许可证的主机级选项： 
  - SQL 服务器企业版 Azure 混合优势
    - 可供具有 SA 或订阅的客户使用。
    - 许可所有可用的物理内核，并享受无限制的虚拟化（最多由主机支持的最大 vCPU）。
        - 有关将 Azure 混合权益应用于 Azure 专用主机的详细信息，请参阅[Azure 混合权益常见问题解答](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)。 
  - 10 月 1 日之前获取的 SQL 服务器许可证
      - SQL Server 企业版同时具有主机级和按 VM 许可证选项。 
      - SQL 服务器标准版只有可用的 VM 许可证选项。 
          - 有关详细信息，请参阅[微软的产品条款](https://www.microsoft.com/licensing/product-licensing/products)。 
  - 如果未选择 SQL Server 专用主机级选项，则 SQL Server AHB 可以在单个 VM 级别选择，就像使用多租户 VM 一样。



## <a name="provisioning"></a>设置  
将 SQL Server VM 预配到专用主机与任何其他 Azure 虚拟机没有什么不同。 可以使用[Azure PowerShell](../dedicated-hosts-powershell.md) [、Azure 门户](../dedicated-hosts-portal.md)和 Azure [CLI](../../linux/dedicated-hosts-cli.md)执行此操作。

将现有 SQL Server VM 添加到专用主机的过程需要停机，但不会影响数据，并且不会丢失数据。 尽管如此，所有数据库（包括系统数据库）都应在移动之前进行备份。

## <a name="virtualization"></a>虚拟化 

专用主机的优点之一是无限虚拟化。 例如，您可以拥有 64 个 vCore 的许可证，但您可以将主机配置为具有 128 个 vCore，因此您可以获得 vCore 的两倍，但只需支付一半的 SQL Server 许可证。 

由于它是您的主机，因此您有资格以 1：2 的比例设置虚拟化。 

## <a name="faq"></a>FAQ

**问：Azure 混合权益如何适用于 Azure 专用主机上的 Windows 服务器/SQL 服务器许可证？**

答：客户可以使用其现有 Windows 服务器和 SQL Server 许可证的值以及软件保障或符合条件的订阅许可证，使用 Azure 混合权益在 Azure 专用主机上支付较低的费率。 Windows 服务器数据中心和 SQL Server 企业版客户在许可整个主机时，可获得无限制的虚拟化（在主机上部署尽可能多的 Windows Server 虚拟机，但需视基础服务器的物理容量而定）并使用 Azure 混合权益。  Azure 专用主机中的所有 Windows 服务器和 SQL 服务器工作负荷也有资格免费享受 Windows 服务器和 SQL Server 2008/R2 的扩展安全更新。 

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL 服务器概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL 服务器常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL 服务器定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL 服务器的发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)


