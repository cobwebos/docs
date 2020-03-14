---
title: 使用 Azure 门户在 Azure 中管理 SQL Server Vm |Microsoft Docs
description: 了解如何访问在 Azure 中托管的 SQL Server VM 的 Azure 门户中的 SQL 虚拟机资源。
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243206"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>使用 Azure 门户管理 Azure 中的 SQL Server Vm

在[Azure 门户](https://portal.azure.com)中， **SQL 虚拟机**资源是一项独立的管理服务。 可以使用它同时查看所有 SQL Server Vm，并修改专用于 SQL Server 的设置： 

![SQL 虚拟机资源](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>备注

- 建议使用**SQL 虚拟机**资源来查看和管理 Azure 中的 SQL Server vm。 但目前， **SQL 虚拟机**资源不支持管理 SQL Server vm 的[支持](virtual-machines-windows-sql-server-2008-eos-extend-support.md)。 若要管理 SQL Server Vm 的支持的设置，请改用 "弃用的[SQL Server 配置" 选项卡](#access-the-sql-server-configuration-tab)。 
- **Sql 虚拟机**资源仅可用于向[sql VM 资源提供程序注册](virtual-machines-windows-sql-register-with-resource-provider.md)的 SQL Server vm。 


## <a name="access-the-sql-virtual-machines-resource"></a>访问 SQL 虚拟机资源
若要访问**SQL 虚拟机**资源，请执行以下操作：

1. 打开 [Azure 门户](https://portal.azure.com)。 
1. 选择“所有服务”。 
1. 在搜索框中输入**SQL 虚拟机**。
1. （可选）：选择 " **SQL 虚拟机**" 旁边的星号将此选项添加到 **"收藏夹"** 菜单。 
1. 选择 " **SQL 虚拟机**"。 

   ![在所有服务中查找 SQL Server 虚拟机](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. 门户会列出订阅中所有可用的 SQL Server Vm。 选择要管理的，以打开 " **SQL 虚拟机**" 资源。 如果 SQL Server VM 未出现，请使用搜索框。 

   ![所有可用的 SQL Server Vm](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   选择 SQL Server VM 会打开**SQL 虚拟机**资源： 


   ![SQL 虚拟机资源](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> **SQL 虚拟机**资源用于专用 SQL Server 设置。 在 "**虚拟机**" 框中选择 vm 的名称，以打开特定于 vm 的设置，但不能排除 SQL Server。 

## <a name="access-the-sql-server-configuration-tab"></a>访问 "SQL Server 配置" 选项卡
" **SQL Server 配置**" 选项卡已被弃用。 目前，这是唯一一种方法，用于管理不能[使用 SQL VM 资源提供程序注册](virtual-machines-windows-sql-register-with-resource-provider.md)的[支持](virtual-machines-windows-sql-server-2008-eos-extend-support.md)SQL Server vm 和 SQL Server vm。

若要访问弃用的**SQL Server 配置**选项卡，请访问**虚拟机**资源。 请使用以下步骤：

1. 打开 [Azure 门户](https://portal.azure.com)。 
1. 选择“所有服务”。 
1. 在搜索框中输入 "**虚拟机**"。
1. （可选）：选择 "**虚拟机**" 旁边的星号将此选项添加到 **"收藏夹"** 菜单。 
1. 选择“虚拟机”。 

   ![搜索虚拟机](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. 门户会列出订阅中的所有虚拟机。 选择要管理的**虚拟机**资源。 如果 SQL Server VM 未出现，请使用搜索框。 
1. 在 "**设置**" 窗格中选择 " **SQL Server 配置**" 以管理你的 SQL Server VM。 

   ![SQL Server 配置](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常见问题](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上 SQL Server 的定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上 SQL Server 的发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)


