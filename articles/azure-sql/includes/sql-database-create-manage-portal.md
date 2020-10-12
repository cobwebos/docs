---
author: stevestein
ms.service: sql-database
ms.subservice: operations
ms.topic: include
ms.date: 08/23/2019
ms.author: sstein
ms.openlocfilehash: 3cc8e836d39a38f950e15d31aeb3e3acdca45acc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84031748"
---
## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>使用 Azure 门户创建和管理 Azure SQL 资源

Azure 门户提供了一个页面，可以在其中管理[所有 Azure SQL 资源](https://go.microsoft.com/fwlink/?linkid=2100641)，包括 SQL 虚拟机。

若要访问 Azure SQL 页面，请从 Azure 门户菜单中选择 Azure SQL，或在任何页面搜索并选择 Azure SQL  。

> [!NOTE]
> Azure SQL 提供了一种快捷简便的方式来访问你在 Azure 门户中的所有 SQL 资源，包括 Azure SQL 数据库中的单个和共用数据库，以及托管它们的逻辑 SQL Server、SQL 托管实例和 SQL 虚拟机。  [Azure SQL](../azure-sql-iaas-vs-paas-what-is-overview.md) 不是服务或资源，而是一系列与 SQL 相关的服务。 

若要管理现有资源，请在列表中选择所需的项。 若要创建新的 Azure SQL 资源，请选择“+ 添加”****。 

![Azure SQL 门户页](./media/sql-database-create-manage-portal/add-azure-sql-resources.png)

选择“+ 添加”后，可通过在任何磁贴上选择“显示详细信息”查看有关不同选项的其他信息********。

![数据库磁贴详细信息](./media/sql-database-create-manage-portal/single-sql-database-deployment-options.png)

有关详细信息，请参阅：

- [创建单一数据库](../database/single-database-create-quickstart.md)
- [创建弹性池](../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [创建托管实例](../managed-instance/instance-create-quickstart.md)
- [创建 SQL 虚拟机](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)