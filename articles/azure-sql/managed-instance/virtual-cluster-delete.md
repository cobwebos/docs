---
title: 删除 SQL 托管实例的托管实例之后删除子网
description: 了解如何在删除 azure SQL 托管实例的托管实例后删除 Azure 虚拟网络。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 87e73ed25ac33777c19c561223f0361d589282b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84696994"
---
# <a name="delete-a-subnet-after-deleting-a-managed-instance-of-sql-managed-instance"></a>删除 SQL 托管实例的托管实例之后删除子网
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文提供了有关如何在删除位于其上的 Azure SQL 托管实例的最后一个托管实例之后手动删除子网的准则。

托管实例将部署到[虚拟群集](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)中。 每个虚拟群集都与一个子网关联。 根据设计，虚拟群集在删除最后一个实例后会保留 12 个小时，使你能够更快地在同一子网中创建托管实例。 可以免费保留空的虚拟群集。 在此期间，不能删除与虚拟群集关联的子网。

如果不想等待 12 个小时，而希望更快地删除虚拟群集及其子网，则可以手动执行此操作。 使用 Azure 门户或虚拟群集 API 手动删除虚拟群集。

> [!IMPORTANT]
> - 若要成功删除，该虚拟群集不能包含任何托管实例。 
> - 删除虚拟群集是一项持续时间长达1.5 小时的长时间运行的操作（请参阅[托管实例管理操作](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations)，了解最新的虚拟群集删除时间）。 在此过程完成之前，虚拟群集仍会显示在门户中。

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>从 Azure 门户删除虚拟群集

若要使用 Azure 门户删除虚拟群集，请搜索虚拟群集资源。

![Azure 门户的屏幕截图，其中突出显示了搜索框](./media/virtual-cluster-delete/virtual-clusters-search.png)

找到要删除的虚拟群集后，请选择此资源，然后选择“删除”。**** 系统会提示你确认删除该虚拟群集。

!["Azure 门户虚拟群集" 仪表板的屏幕截图，其中突出显示了 "删除" 选项](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Azure 门户通知会显示确认，指出删除虚拟群集的请求已成功提交。 删除操作的持续时间大约为1.5 小时，在此期间，虚拟群集仍将在门户中可见。 完成此过程后，虚拟群集将不再可见，与之关联的子网并将被释放以供重复使用。

> [!TIP]
> 如果没有托管实例显示在虚拟群集中，而你无法删除虚拟群集，请确保没有正在进行的实例部署。 这包括已启动的和已取消的仍在进行的部署。 这是因为这些操作仍将使用虚拟群集，并将其锁定为删除。 查看部署该实例的资源组的 "**部署**" 选项卡将指示正在进行的任何部署。 在这种情况下，请等待部署完成，删除该托管实例，然后删除该虚拟群集。

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>使用 API 删除虚拟群集

若要通过 API 删除虚拟群集，请使用[虚拟群集删除方法](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)中指定的 URI 参数。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是 AZURE SQL 托管实例？](sql-managed-instance-paas-overview.md)。
- 了解[SQL 托管实例中的连接体系结构](connectivity-architecture-overview.md)。
- 了解如何[修改 SQL 托管实例的现有虚拟网络](vnet-existing-add-subnet.md)。
- 有关演示如何创建虚拟网络、创建托管实例和从数据库备份还原数据库的教程，请参阅[创建托管实例](instance-create-quickstart.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](custom-dns-configure.md)。
