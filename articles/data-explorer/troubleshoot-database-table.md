---
title: 无法在 Azure 数据资源管理器中创建或删除数据库或表
description: 本文介绍在 Azure 数据资源管理器中创建和删除数据库和表的故障排除步骤。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 62eccebab81745f85450390f64b6b22f3c17b32e
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758319"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>故障排除：无法在 Azure 数据资源管理器中创建或删除数据库或表

在 Azure 数据资源管理器中，经常会使用数据库和表。 本文针对可能出现的问题提供了疑难解答步骤。

## <a name="creating-a-database"></a>创建数据库

1. 确保有足够的权限。 若要创建数据库，你必须是 Azure 订阅的“参与者”或“所有者”角色。 如有必要，请与订阅管理员联系，请他们将你添加到相应的角色。

1. 确保数据库名称不存在名称验证错误。 该名称必须是字母数字，最大长度为 260 个字符。

1. 请确保数据库保留期和缓存的值都在允许范围内。 保留期必须介于 1 到 36500 天（100 年）之间。 缓存必须介于 1 和为保留期设置的最大值之间。

## <a name="deleting-or-renaming-a-database"></a>删除或重命名数据库

请确保具有足够的权限。 若要删除或重命名数据库，你必须是 Azure 订阅的“参与者”或“所有者”角色。 如有必要，请与订阅管理员联系，请他们将你添加到相应的角色。

## <a name="creating-a-table"></a>创建表

1. 请确保具有足够的权限。 若要创建表，你必须是数据库中的“数据库管理员”或“数据库用户”角色，或者是 Azure 订阅的“参与者”或“所有者”角色。 如有必要，请与订阅或群集管理员联系，以便他们可以将你添加到相应的角色。

    有关权限的详细信息，请参阅[管理数据库权限](manage-database-permissions.md)。

1. 请确保不存在具有相同名称的表。 如果它存在，则你可以：使用不同的名称; 创建表重命名现有表 (需要*表管理员*角色); 或删除现有表 (需要*数据库管理员*角色)。 使用以下命令。

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>删除或重命名表

请确保具有足够的权限。 若要删除或重命名表，你必须是数据库中的“数据库管理员”或“表管理员”角色。 如有必要，请与订阅或群集管理员联系，以便他们可以将你添加到相应的角色。

有关权限的详细信息，请参阅[管理数据库权限](manage-database-permissions.md)。

## <a name="general-guidance"></a>一般指南

1. 查看 [Azure 服务健康状况仪表板](https://azure.microsoft.com/status/)。 在尝试使用数据库或表的区域查找 Azure 数据资源管理器的状态。

    如果状态不佳（绿色复选标记），请在状态改善后重试。

1. 解决问题时如仍需帮助，请打开 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)中的支持请求。

## <a name="next-steps"></a>后续步骤

[检查群集运行状况](check-cluster-health.md)