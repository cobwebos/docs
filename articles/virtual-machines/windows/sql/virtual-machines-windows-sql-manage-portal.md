---
title: 使用 Azure 门户管理 Azure 中的 SQL 服务器 VM |微软文档
description: 了解如何访问 Azure 门户中托管在 Azure 上的 SQL Server VM 的 SQL 虚拟机资源。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 244ad7d079fd7baf25f8079557576c42d25ca785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243206"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>使用 Azure 门户管理 Azure 中的 SQL 服务器 VM

在[Azure 门户](https://portal.azure.com)中 **，SQL 虚拟机**资源是一个独立的管理服务。 您可以使用它同时查看所有 SQL Server VM 并修改专用于 SQL Server 的设置： 

![SQL 虚拟机资源](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>备注

- 我们建议您使用**SQL 虚拟机**资源在 Azure 中查看和管理 SQL Server VM。 但是，目前 SQL**虚拟机**资源不支持[管理支持终止](virtual-machines-windows-sql-server-2008-eos-extend-support.md)SQL Server VM。 要管理支持终止 SQL Server VM 的设置，请使用已弃用的[SQL Server 配置选项卡](#access-the-sql-server-configuration-tab)。 
- **SQL 虚拟机**资源仅适用于[已注册到 SQL VM 资源提供程序的](virtual-machines-windows-sql-register-with-resource-provider.md)SQL Server VM。 


## <a name="access-the-sql-virtual-machines-resource"></a>访问 SQL 虚拟机资源
要访问**SQL 虚拟机**资源，可以执行以下操作：

1. 打开[Azure 门户](https://portal.azure.com)。 
1. 选择**所有服务**。 
1. 在搜索框中输入**SQL 虚拟机**。
1. （可选）：选择**SQL 虚拟机**旁边的星形，将此选项添加到**收藏夹**菜单中。 
1. 选择**SQL 虚拟机**。 

   ![在所有服务中查找 SQL Server 虚拟机](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. 门户列出了订阅中可用的所有 SQL Server VM。 选择要管理以打开**SQL 虚拟机**资源。 如果 SQL Server VM 未显示，请使用搜索框。 

   ![所有可用的 SQL 服务器 VM](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   选择 SQL Server VM 将打开**SQL 虚拟机**资源： 


   ![SQL 虚拟机资源](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> **SQL 虚拟机**资源用于专用 SQL Server 设置。 在**虚拟机**框中选择 VM 的名称以打开特定于 VM 但不特定于 SQL Server 的设置。 

## <a name="access-the-sql-server-configuration-tab"></a>访问 SQL 服务器配置选项卡
已弃用**SQL Server 配置**选项卡。 此时，它是管理[支持终止](virtual-machines-windows-sql-server-2008-eos-extend-support.md)SQL Server VM 和尚未[向 SQL VM 资源提供程序注册的](virtual-machines-windows-sql-register-with-resource-provider.md)SQL Server VM 的唯一方法。

要访问已弃用的**SQL Server 配置**选项卡，请访问**虚拟机**资源。 请使用以下步骤：

1. 打开[Azure 门户](https://portal.azure.com)。 
1. 选择**所有服务**。 
1. 在搜索框中输入**虚拟机**。
1. （可选）：选择**虚拟机**旁边的星形，将此选项添加到**收藏夹**菜单。 
1. 选择**虚拟机**。 

   ![搜索虚拟机](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. 门户列出订阅中的所有虚拟机。 选择要管理以打开**虚拟机**资源。 如果 SQL Server VM 未显示，请使用搜索框。 
1. 在 **"设置"** 窗格中选择**SQL Server 配置**以管理 SQL Server VM。 

   ![SQL Server 配置](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL 服务器概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL 服务器常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL 服务器定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL 服务器的发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)


