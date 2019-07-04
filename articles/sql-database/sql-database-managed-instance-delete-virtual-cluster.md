---
title: 删除子网后删除 Azure SQL 数据库托管实例 |Microsoft Docs
description: 了解如何删除 Azure 虚拟网络后删除 Azure SQL 数据库托管实例。
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
ms.date: 06/26/2019
ms.openlocfilehash: 4679ecda210fa78aad4315bc6602b67dd1795ce9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67427972"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>删除子网后删除 Azure SQL 数据库托管实例

本文提供有关如何删除最后一个 Azure SQL 数据库托管实例驻留在其中后，手动删除子网的指导原则。

使用 SQL 数据库[虚拟群集](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)以包含已删除托管的实例。 虚拟群集仍然存在实例删除，以使您能够快速创建托管的实例位于同一子网后的 12 小时。 不保留为空的虚拟群集收费。 在此期间，不能删除与虚拟群集关联的子网。

如果您不想等待 12 个小时，并想要立即删除虚拟群集和它的子网，你可以手动执行此操作。 通过使用 Azure 门户或虚拟群集 API 手动删除虚拟群集。

> [!NOTE]
> 若要成功删除，该虚拟群集不能包含任何托管实例。

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Azure 门户中删除虚拟群集

若要使用 Azure 门户删除虚拟群集，请搜索虚拟群集资源。

![Azure 门户中，使用搜索框中突出显示的屏幕截图](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

找到要删除的虚拟群集后，选择此资源，并选择**删除**。 系统会提示确认虚拟群集删除。

![Azure 门户中虚拟的屏幕截图群集仪表板，其中突出显示的删除选项](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Azure 门户的通知区域显示虚拟群集已删除的确认。 成功删除虚拟群集立即释放以供重复使用的子网。

> [!TIP]
> 如果没有虚拟群集中所示的托管实例，并且你将无法删除虚拟群集，请确保，你没有正在进行的实例部署正在进行中。 这包括已启动，并且取消仍在进行的部署。 查看的部署的资源组实例的选项卡已部署到将指示正在进行中的任何部署。 在这种情况下，等待部署完成，删除托管的实例，然后选择虚拟群集。

## <a name="delete-virtual-cluster-by-using-the-api"></a>通过使用 API 删除虚拟群集

若要删除通过 API 虚拟群集，请使用 URI 参数中指定[虚拟群集删除方法](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete)。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是托管实例？](sql-database-managed-instance.md)。
- 了解[托管实例中的连接体系结构](sql-database-managed-instance-connectivity-architecture.md)。
- 了解如何[修改托管实例的现有虚拟网络](sql-database-managed-instance-configure-vnet-subnet.md)
- 有关如何创建虚拟网络、创建托管实例，以及从数据库备份还原数据库的教程，请参阅[创建 Azure SQL 数据库托管实例](sql-database-managed-instance-get-started.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)。
