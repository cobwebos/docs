---
author: stevestein
ms.service: sql-database
ms.subservice: operations
ms.topic: include
ms.date: 08/23/2019
ms.author: sstein
ms.openlocfilehash: 3cc8e836d39a38f950e15d31aeb3e3acdca45acc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84031748"
---
## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>使用 Azure 门户创建和管理 Azure SQL 资源

Azure 门户提供了一个页面，可以在其中管理[所有 Azure SQL 资源](https://go.microsoft.com/fwlink/?linkid=2100641)，包括 SQL 虚拟机。

若要访问**AZURE sql**页面，请在 "Azure 门户" 菜单中选择 " **azure sql** "，或在任何页面中搜索并选择 " **azure sql** "。

> [!NOTE]
> **AZURE sql**提供一种便捷的方式来访问 Azure 门户中的所有 SQL 资源，其中包括 Azure SQL 数据库中的单个和共用数据库，以及托管它们的逻辑 sql 服务器、Sql 托管实例和 sql 虚拟机。  [AZURE SQL](../azure-sql-iaas-vs-paas-what-is-overview.md)不是服务或资源，而是一系列与 SQL 相关的服务。 

若要管理现有资源，请在列表中选择所需的项。 若要创建新的 Azure SQL 资源，请选择“+ 添加”。 

![Azure SQL 门户页](./media/sql-database-create-manage-portal/add-azure-sql-resources.png)

选择“+ 添加”后，可通过在任何磁贴上选择“显示详细信息”查看有关不同选项的其他信息 。

![数据库磁贴详细信息](./media/sql-database-create-manage-portal/single-sql-database-deployment-options.png)

有关详细信息，请参阅：

- [创建单一数据库](../database/single-database-create-quickstart.md)
- [创建弹性池](../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [创建托管实例](../managed-instance/instance-create-quickstart.md)
- [创建 SQL 虚拟机](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)