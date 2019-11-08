---
title: 删除托管实例之后删除子网
description: 了解如何在删除 Azure SQL 数据库托管实例后删除 Azure 虚拟网络。
services: sql-database
ms.service: sql-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496d67a73207fd17182c31c5adad25c1fbe60c4f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820463"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>在删除 Azure SQL 数据库托管实例后删除子网

本文提供有关如何在删除子网中的最后一个 Azure SQL 数据库托管实例后，手动删除该子网的指导。

托管的实例部署到[虚拟群集](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)中。 每个虚拟群集都与一个子网关联。 在最后一次实例删除后，虚拟群集将按设计持续12小时，使你能够更快地在同一子网中创建托管实例。 可以免费保留空的虚拟群集。 在此期间，不能删除与虚拟群集关联的子网。

如果不想等待12小时，并且更早删除虚拟群集及其子网，则可以手动执行此操作。 请通过 Azure 门户或虚拟群集 API 手动删除虚拟群集。

> [!IMPORTANT]
> - 若要成功删除，该虚拟群集不能包含任何托管实例。 
> - 删除虚拟群集是一项持续时间长达1.5 小时的长时间运行的操作（有关最新虚拟群集删除时间，请参阅[托管实例管理操作](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations)），在此期间，虚拟群集仍将显示在门户中进程已完成。

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>在 Azure 门户中删除虚拟群集

若要使用 Azure 门户删除虚拟群集，请搜索虚拟群集资源。

![Azure 门户的屏幕截图，其中突出显示了搜索框](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

找到要删除的虚拟群集后，请选择此资源，然后选择“删除”。 系统会提示你确认删除该虚拟群集。

![Azure 门户“虚拟群集”仪表板的屏幕截图，其中突出显示了“删除”选项](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Azure 门户通知会显示一个确认，指出已成功提交删除虚拟群集的请求。 删除操作的持续时间大约为1.5 小时，在此期间，虚拟群集仍将在门户中可见。 完成此过程后，虚拟群集将不再可见，并将释放与之关联的子网以供重复使用。

> [!TIP]
> 如果没有托管实例显示在虚拟群集中，而你无法删除虚拟群集，请确保没有正在进行的实例部署。 这包括已启动的和已取消的仍在进行的部署。 这是因为这些操作仍将使用虚拟群集来阻止其删除。 实例部署到的资源组的“查看部署”选项卡会指示正在进行的任何部署。 在这种情况下，等待部署完成，删除托管实例和虚拟群集。

## <a name="delete-virtual-cluster-by-using-the-api"></a>使用 API 删除虚拟群集

若要通过 API 删除虚拟群集，请使用[虚拟群集删除方法](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)中指定的 URI 参数。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是托管实例？](sql-database-managed-instance.md)。
- 了解[托管实例中的连接体系结构](sql-database-managed-instance-connectivity-architecture.md)。
- 了解如何[修改托管实例的现有虚拟网络](sql-database-managed-instance-configure-vnet-subnet.md)
- 有关如何创建虚拟网络、创建托管实例，以及从数据库备份还原数据库的教程，请参阅[创建 Azure SQL 数据库托管实例](sql-database-managed-instance-get-started.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)。
