---
title: "为 Azure SQL 数据库启动自动优化 | Microsoft Docs"
description: "可轻松对 Azure SQL 数据库启用自动优化。"
services: sql-database
documentationcenter: 
author: vvasic
manager: drasumic
editor: vvasic
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 06/05/2016
ms.author: vvasic
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: b391b1f7aa37c5a06fc320ce892534187deb4959
ms.contentlocale: zh-cn
ms.lasthandoff: 06/13/2017


---
# <a name="enable-automatic-tuning"></a>启用自动优化

Azure SQL 数据库是自动托管的数据服务，可持续监视查询并识别可为改善工作负荷性能执行的操作。 可查看建议并手动应用，或让 Azure SQL 数据库自动应用正确的操作 - 这称为“自动优化模式”。 可在服务器或数据库级别启用自动优化。

## <a name="enable-automatic-tuning-on-server"></a>对服务器启用自动优化

若要对 Azure SQL 数据库服务器启用自动优化，请在 Azure 门户中导航到该服务器，然后选择菜单中的“自动优化”。 选择想要启用的自动优化选项，然后选择“应用”：

![服务器](./media/sql-database-automatic-tuning-enable/server.png)

服务器上的自动优化选项将应用到服务器上的所有数据库。 默认情况下，所有数据库将从其父服务器继承配置，但可替代此配置并为每个数据库单独指定配置。

## <a name="configure-automatic-tuning-on-database"></a>对数据库配置自动优化

Azure 门户支持为每个数据库单独指定自动优化配置。

> [!NOTE]
> 常规建议是在服务器级别管理自动优化配置，以便为每个数据库自动应用相同的配置设置。 如果数据库与同一服务器上的其他数据库不同，则在单个数据库上配置自动优化。
>

若要对单个数据库启用自动优化，请在 Azure 门户中导航到该数据库，然后选择“自动优化”。 通过选中复选框，可将单个数据库配置为从数据库继承设置，或者还可单独为数据库指定配置。

![数据库](./media/sql-database-automatic-tuning-enable/database.png)

选择相应配置后，单击“应用”。

## <a name="next-steps"></a>后续步骤
* 请阅读[自动优化文章](sql-database-automatic-tuning.md)，详细了解自动优化及其如何帮助提高性能。
* 请参阅[性能建议](sql-database-advisor.md)，了解 Azure SQL 数据库性能建议的概述。
* 若要了解排名靠前的查询的性能影响，请参阅[查询性能见解](sql-database-query-performance.md)。

