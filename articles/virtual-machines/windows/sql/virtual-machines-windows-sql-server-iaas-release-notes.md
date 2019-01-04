---
title: Azure VM 上的 SQL Server 发行说明 | Microsoft Docs
description: 了解 Azure VM 上的 SQL Server 的新增功能和改进
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2018
ms.author: mathoma
ms.openlocfilehash: 52036d8f5d25fc4a4f2d1b602428e9cba4762b7f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993140"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Azure 虚拟机上的 SQL Server 发行说明

Azure 允许你使用内置的 SQL Server 映像部署虚拟机。 本文列出了在 Azure 虚拟机上部署的 SQL Server 的最新版本中可以预期的新增功能和改进。 


## <a name="november-2018"></a>2018 年 11 月
- **新的 SQL 资源提供程序**：为 SQL VM 添加了一个新的资源提供程序，可用来更好地管理 VM。 有关注册 VM 的详细信息，请参阅[将现有 SQL VM 注册到新的资源提供程序](virtual-machines-windows-sql-ahb.md#register-existing-sql-vm-with-new-resource-provider)。
- **切换许可模型**：你现在可以使用 Azure CLI 或 Powershell 在“按使用情况付费”与“自带许可证”模型之间切换。 有关详细信息，请参阅[如何更改 SQL VM 的许可模型](virtual-machines-windows-sql-ahb.md)



## <a name="additional-resources"></a>其他资源

**Windows VM**：

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)。
* [设置 SQL Server Windows VM](virtual-machines-windows-portal-sql-server-provision.md)
* [将数据库迁移到 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)
* [Azure 虚拟机中 SQL Server 的高可用性和灾难恢复](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure 虚拟机中 SQL Server 的性能最佳实践](virtual-machines-windows-sql-performance.md)
* [Azure 虚拟机中的 SQL Server 的应用程序模式和开发策略](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**：

* [Linux VM 上的 SQL Server 概述](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [设置 SQL Server Linux VM](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [常见问题 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [“Linux 上的 SQL Server”文档](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
