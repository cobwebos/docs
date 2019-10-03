---
author: stevestein
ms.service: sql-database
ms.subservice: operations
ms.topic: include
ms.date: 08/23/2019
ms.author: sstein
ms.openlocfilehash: 9e189d0582d2e81193c892cb349b475ed0d68df8
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014741"
---
## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>利用 Azure 门户创建和管理 Azure SQL 资源

Azure 门户提供了一个页面, 可在其中管理[所有 AZURE sql 资源](https://go.microsoft.com/fwlink/?linkid=2100641)(包括 SQL 虚拟机)。

若要访问 " **AZURE sql 资源**" 页, 请在 Azure 门户的左侧菜单中选择 " **azure sql** "。 如果**AZURE sql**不在列表中, 请选择 "**所有服务**", 然后在搜索框中键入 " *Azure sql* "。

> [!NOTE]
> **AZURE sql**提供一种便捷的方式来访问所有 SQL 数据库、弹性池、数据库服务器、sql 托管实例和 sql 虚拟机。 Azure SQL 不是服务或资源。 

若要管理现有资源, 请在列表中选择所需的项。 若要创建新的 Azure SQL 资源, 请选择 " **+ 添加**"。 

![Azure SQL 门户页](../media/sql-database-technical-overview/azure-sql.png)

选择 " **+ 添加**" 后, 通过选择 "在任何磁贴上**显示详细**信息" 来查看有关不同选项的其他信息。

![数据库磁贴详细信息](../media/sql-database-technical-overview/create-single-database.png)

有关详细信息，请参阅：

- [创建单一数据库](../sql-database-single-database-get-started.md)
- [创建弹性池](../sql-database-elastic-pool.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [创建托管实例](../sql-database-managed-instance-get-started.md)
- [创建 SQL 虚拟机](../../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md)