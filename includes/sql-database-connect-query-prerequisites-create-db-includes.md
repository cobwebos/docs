---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 01/28/2019
ms.author: genemi
ms.openlocfilehash: edeaa932abe4d1882f957d0ed26aaddd97dabe3f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198055"
---
<!-- sql-database-connect-query-prerequisites-create-db-includes.md -->

- 置于[逻辑服务器](https://docs.microsoft.com/azure/sql-database/sql-database-single-index)或[托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)上的 Azure SQL 数据库。 可以使用以下方法之一来创建数据库：

| 逻辑服务器 | 托管实例 |
| --- | --- |
| [门户](../articles/sql-database/sql-database-get-started-portal.md) | [门户](../articles/sql-database/sql-database-managed-instance-get-started.md) |
| [CLI](../articles/sql-database/sql-database-get-started-cli.md) | |
| [PowerShell](../articles/sql-database/sql-database-get-started-powershell.md) | |

- **仅逻辑服务器** - 一项已配置的服务器级别防火墙规则，可以通过它连接到逻辑服务器。 有关详细信息，请参阅[创建服务器级防火墙规则](../articles/sql-database/sql-database-get-started-portal-firewall.md)。
- **仅托管实例** - 通过访问托管实例的计算机配置的连接。 可以使用以下选项：
  - 可以访问该实例的托管实例所在的 Azure VNet 中的 [Azure 虚拟机](../articles/sql-database/sql-database-managed-instance-configure-vm.md)。
  - 计算机上的[点到站点连接](../articles/sql-database/sql-database-managed-instance-configure-p2s.md)，可以通过它将计算机加入托管实例所在的 VNet，并将托管实例用作网络中的任何其他 SQL Server。
