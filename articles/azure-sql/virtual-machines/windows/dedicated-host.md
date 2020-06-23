---
title: 在 Azure 专用主机上运行 SQL Server VM
description: 了解如何在 Azure 专用主机上运行 SQL Server VM。
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
ms.openlocfilehash: 1c16c2cdae671a9b18a34b88b9490b5b61c24c8e
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2020
ms.locfileid: "84220226"
---
# <a name="run-sql-server-vm-on-an-azure-dedicated-host"></a>在 Azure 专用主机上运行 SQL Server VM 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文详细介绍了如何将 SQL Server 虚拟机 (VM) 和 [Azure 专用主机](/azure/virtual-machines/windows/dedicated-hosts)结合使用。 有关 Azure 专用主机的其他信息，请参阅博客文章 [Azure 专用主机简介](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/)。 

## <a name="overview"></a>概述
[Azure 专用主机](/azure/virtual-machines/windows/dedicated-hosts) 是一种提供物理服务器的服务，物理服务器可托管专用于一个 Azure 订阅的一个或多个虚拟机。 专用主机是 Microsoft 数据中心使用的物理服务器并作为资源提供。 你可以在区域、可用性区域和容错域中预配专用主机。 然后，可以按照最能满足你的需要的配置将 VM 直接放入预配的主机中。

## <a name="limitations"></a>限制

- 虚拟机规模集目前在专用主机上不受支持。
- 支持以下 VM 系列：DSv3 和 ESv3。 

## <a name="licensing"></a>授权

将 SQL Server VM 放置在 Azure 专用主机中后，有两个不同的许可选项可供选择。 

  - **SQL VM 许可**：这是现有的许可选项，选择它后，可以单独为每个 SQL Server VM 许可付费。 
  - **专用主机许可**：这是适用于 Azure 专用主机的新许可模型，这种模式将 SQL Server 许可证进行捆绑并在主机级别付费。 


用于使用现有 SQL Server 许可证的主机级选项： 
  - SQL Server Enterprise Edition Azure 混合权益 (AHB)
    - 适用于具有 SA 或订阅的客户。
    - 授权使用所有可用的物理核心，可享受无限的虚拟化（最多可达主机支持的最大 vCPU 数）。
        - 有关将 AHB 应用到 Azure 专用主机的详细信息，请参阅 [Azure 混合权益常见问题解答](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)。 
  - 10 月 1 日前获取的 SQL Server 许可证
      - SQL Server Enterprise Edition 同时提供主机级别和按 VM 的许可证选项。 
      - Microsoft SQL Server Standard Edition 仅提供按 VM 的许可证选项。 
          - 有关详细信息，请参阅 [Microsoft 产品条款](https://www.microsoft.com/licensing/product-licensing/products)。 
  - 如果未选择 SQL Server 专用主机级别选项，则可以在单个 VM 级别选择 SQL Server AHB，就像多租户 VM 的操作方式一样。



## <a name="provisioning"></a>设置  
将 SQL Server VM 预配到专用主机的操作与其他任何 Azure 虚拟机一样。 可以使用 [Azure PowerShell](../../../virtual-machines/windows/dedicated-hosts-powershell.md)、[Azure 门户](../../../virtual-machines/windows/dedicated-hosts-portal.md)和 [Azure CLI](../../../virtual-machines/linux/dedicated-hosts-cli.md) 执行此操作。

将现有 SQL Server VM 添加到专用主机的过程需要停机，但不会影响数据，也不会造成数据丢失。 尽管如此，所有数据库（包括系统数据库）在移动之前都应进行备份。

## <a name="virtualization"></a>虚拟化 

专用主机的一个优点是无限虚拟化。 例如，在拥有 64 个 vCore 许可证的情况下可将主机配置为拥有 128 个 vcore，因此可以配置双倍的 vCore，但只需支付 SQL Server 许可证费用的一半。 

由于是你的主机，因此你有权将虚拟化设置为 1:2 的比例。 

## <a name="faq"></a>常见问题解答

**问：如何对 Azure 专用主机上的 Windows Server/SQL Server 许可证使用 Azure 混合权益？**

答：通过使用 Azure 混合权益，客户可以将现有 Windows Server 和 SQL Server 许可证的价值与软件保障或合格的订阅许可证结合利用，从而在 Azure 专用主机上按折扣费率付费。 Windows Server Datacenter 和 SQL Server Enterprise Edition 客户在获得整个主机的许可并使用 Azure 混合权益后，可获得无限虚拟化（根据基础服务器的物理容量，在主机上部署尽可能多的 Windows Server 虚拟机）。  Azure 专用主机中的所有 Windows Server 和 SQL Server 工作负载还有资格获得 Windows Server 和 SQL Server 2008/R2 的扩展安全更新，而无需额外付费。 

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常见问题解答](frequently-asked-questions-faq.md)
* [Windows VM 上的 SQL Server 定价指南](pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](doc-changes-updates-release-notes.md)


