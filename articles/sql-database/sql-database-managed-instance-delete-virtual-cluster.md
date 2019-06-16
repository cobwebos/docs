---
title: 删除 Azure SQL 数据库托管实例后删除 VNet | Microsoft Docs
description: 了解如何在删除 Azure SQL 数据库托管实例后删除 VNet。
services: sql-database
ms.service: sql-database
ms.subservice: management
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: 61f6c25031c4906e65c2f75a7679600741e8311a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65791379"
---
# <a name="delete-subnet-after-deleting-azure-sql-database-managed-instance"></a>删除 Azure SQL 数据库托管实例后删除子网

本文提供有关如何在删除子网中的最后一个 Azure SQL 数据库托管实例后，手动删除该子网的指导。

包含已删除的托管实例的[虚拟群集](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)在删除该实例后，将保留 12 个小时。 该虚拟群集将按设计保持活动状态，以便更快地在同一子网中创建托管实例。 保留为空的虚拟群集是免费。 在此期间，无法删除与该虚拟群集关联的子网。

可以通过手动删除虚拟群集，来立即释放空虚拟群集使用的子网。 可以通过 Azure 门户或虚拟群集 API 删除虚拟群集。

> [!NOTE]
> 若要成功删除，该虚拟群集不能包含任何托管实例。

## <a name="delete-virtual-cluster-from-azure-portal"></a>在 Azure 门户中删除虚拟群集

若要使用 Azure 门户删除虚拟群集，请使用内置的搜索功能搜索虚拟群集资源。

![搜索虚拟群集。](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

找到要删除的虚拟群集后，请选择此资源，然后选择“删除”选项。 系统会提示你确认删除该虚拟群集。

![删除虚拟群集。](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Azure 门户通知中会提供已删除虚拟群集的确认消息。 成功删除虚拟群集后，会立即释放子网，供将来重复使用。

## <a name="delete-virtual-cluster-using-api"></a>使用 API 删除虚拟群集

若要通过 API 删除虚拟群集，请使用[虚拟群集删除方法](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)中指定的 URI 参数。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是托管实例？](sql-database-managed-instance.md)。
- 了解[托管实例中的连接体系结构](sql-database-managed-instance-connectivity-architecture.md)。
- 了解如何[修改托管实例的现有虚拟网络](sql-database-managed-instance-configure-vnet-subnet.md)
- 有关如何创建虚拟网络、创建托管实例，以及从数据库备份还原数据库的教程，请参阅[创建 Azure SQL 数据库托管实例](sql-database-managed-instance-get-started.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)。
