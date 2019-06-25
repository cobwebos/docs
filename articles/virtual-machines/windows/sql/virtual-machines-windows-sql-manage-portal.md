---
title: 管理 Azure 中使用 Azure 门户中的 SQL Server Vm |Microsoft Docs
description: 了解如何在 Azure 上托管的 SQL Server VM 访问 Azure 门户中的 SQL 虚拟机资源。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e2a807bbd6baeb2f14a6d36f5d98a28d48725449
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082711"
---
# <a name="manage-sql-server-vms-in-azure-using-the-azure-portal"></a>管理 Azure 中使用 Azure 门户中的 SQL Server Vm

没有新的访问点来管理 Azure 上使用 SQL Server VM [Azure 门户](https://portal.azure.com)。 

**SQL 虚拟机**资源现在是一种独立的管理服务，可用于所有 SQL Server 虚拟机的同时查看和修改专用于 SQL Server 的设置： 

![SQL 虚拟机资源](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>备注

- **SQL 虚拟机**资源是用于查看和管理 SQL Server Vm 的建议的方法。 但是，目前**SQL 虚拟机**资源不支持的管理[结束 (EOS) 支持](virtual-machines-windows-sql-server-2008-eos-extend-support.md)SQL Server Vm。 若要管理 EOS SQL Server Vm 的设置，请使用已弃用[SQL Server 配置选项卡](#access-sql-server-configuration-tab)相反。 
- **SQL 虚拟机**仅适用于 SQL Server Vm 的具有资源，正在[向 SQL 虚拟机资源提供程序注册](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-the-sql-vm-resource-provider)。 


## <a name="access-sql-virtual-machine-resource"></a>访问 SQL 虚拟机资源
若要访问 SQL 虚拟机资源，请执行以下操作：

1. 打开 [Azure 门户](https://portal.azure.com)。 
1. 选择“所有服务”  。 
1. 类型`SQL virtual machines`在搜索框中。
1. （可选）：下一步选择星形**SQL 虚拟机**将此选项添加到收藏夹菜单。 
1. 选择**SQL 虚拟机**。 

   ![在所有服务中查找 SQL VM 的虚拟机](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. 这将列出所有 SQL Server Vm 在订阅中可用。 选择你想要管理以启动的一个**SQL 虚拟机**资源。 使用搜索框中，如果 SQL Server VM 并不十分明显。 

![所有可用的 SQL Vm](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

选择 SQL Server VM 将打开**SQL 虚拟机**资源： 


![SQL 虚拟机资源](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

  > [!TIP]
  > **SQL 虚拟机**资源是为专用的 SQL Server 设置。 选择在 VM 的名称**虚拟机**字段用于导航到特定于 VM，但不专用于 SQL Server 的设置。 

## <a name="access-sql-server-configuration-tab"></a>访问 SQL Server 配置选项卡
SQL Server 配置选项卡已被弃用。 在此期间，它是唯一的方法来管理[结束 (EOS) 支持](virtual-machines-windows-sql-server-2008-eos-extend-support.md)SQL Server Vm 和 SQL Server Vm 还没有[向 SQL 虚拟机资源提供程序注册](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-the-sql-vm-resource-provider)。

若要访问不推荐使用的 SQL server 配置选项卡，您将需要导航到**虚拟机**资源。 为此，请执行以下操作：

1. 打开 [Azure 门户](https://portal.azure.com)。 
1. 选择“所有服务”  。 
1. 类型`virtual machines`在搜索框中。
1. （可选）：下一步选择星形**虚拟机**将此选项添加到收藏夹菜单。 
1. 选择“虚拟机”。  

   ![搜索虚拟机](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. 这将列出订阅中的所有虚拟机。 选择你想要管理以启动的一个**虚拟机**资源。 使用搜索框中，如果 SQL Server VM 并不十分明显。 
1. 选择**SQL Server 配置**中**设置**窗格可以管理您的 SQL Server。 

![SQL Server 配置](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL Server 定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)


