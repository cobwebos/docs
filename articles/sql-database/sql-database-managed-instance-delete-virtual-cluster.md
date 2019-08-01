---
title: 删除 Azure SQL 数据库托管实例之后删除子网 |Microsoft Docs
description: 了解如何在删除 Azure SQL 数据库托管实例后删除 Azure 虚拟网络。
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: f6e0b55ad2fbd9b4c45dbd1facaebd4750314c63
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567529"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>删除 Azure SQL 数据库托管实例之后删除子网

本文提供了有关如何在删除位于其上的最后一个 Azure SQL 数据库托管实例之后手动删除子网的准则。

SQL 数据库使用[虚拟群集](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)来包含已删除的托管实例。 虚拟群集在实例删除后会持续12小时, 使你能够在同一子网中快速创建托管实例。 保留空虚拟群集不收取任何费用。 在此期间，不能删除与虚拟群集关联的子网。

如果你不想等待12小时, 并且想要立即删除虚拟群集及其子网, 则可以手动执行此操作。 使用 Azure 门户或虚拟群集 API 手动删除虚拟群集。

> [!NOTE]
> 若要成功删除，该虚拟群集不能包含任何托管实例。

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>从 Azure 门户删除虚拟群集

若要使用 Azure 门户删除虚拟群集, 请搜索虚拟群集资源。

!["搜索" 框突出显示的 Azure 门户屏幕截图](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

找到要删除的虚拟群集后, 选择此资源, 然后选择 "**删除**"。 系统会提示确认虚拟群集删除。

![Azure 门户虚拟群集仪表板的屏幕截图, 其中突出显示了删除选项](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

"Azure 门户通知" 区域显示已确认虚拟群集已删除。 成功删除虚拟群集后, 会立即释放子网以供重复使用。

> [!TIP]
> 如果虚拟群集中没有显示托管实例, 并且无法删除虚拟群集, 请确保没有正在进行的实例部署。 这包括仍在进行中的已启动和已取消的部署。 部署该实例的资源组的 "查看部署" 选项卡将指示正在进行的任何部署。 在这种情况下, 等待部署完成后, 删除托管实例和虚拟群集。

## <a name="delete-virtual-cluster-by-using-the-api"></a>使用 API 删除虚拟群集

若要通过 API 删除虚拟群集, 请使用[虚拟群集 delete 方法](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)中指定的 URI 参数。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是托管实例？](sql-database-managed-instance.md)。
- 了解[托管实例中的连接体系结构](sql-database-managed-instance-connectivity-architecture.md)。
- 了解如何[修改托管实例的现有虚拟网络](sql-database-managed-instance-configure-vnet-subnet.md)
- 有关如何创建虚拟网络、创建托管实例，以及从数据库备份还原数据库的教程，请参阅[创建 Azure SQL 数据库托管实例](sql-database-managed-instance-get-started.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)。
