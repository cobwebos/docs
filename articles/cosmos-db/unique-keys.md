---
title: 在 Azure Cosmos DB 中使用唯一键
description: 了解如何使用 Azure Cosmos 数据库中的唯一键
author: rockboyfor
ms.author: v-yeche
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 04/08/2019
ms.date: 04/15/2019
ms.reviewer: sngun
ms.openlocfilehash: 3c5e8a2c85898175772dc353258e77fc8e0a74f2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115717"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Azure Cosmos DB 中的唯一键约束

唯一键在 Azure Cosmos 容器中添加一个数据完整性层。 在创建 Azure Cosmos 容器时将创建唯一键策略。 使用唯一键可确保逻辑分区内一个或多个值的唯一性。 此外，可以保证每个[分区键](partition-data.md)的唯一性。 

使用唯一键策略创建容器后，该策略会根据唯一键约束的指定，阻止在逻辑分区中执行会导致重复的创建新项或更新现有项的操作。 分区键与唯一键的组合可保证某个项在容器范围内的唯一性。

例如，假设某个 Azure Cosmos 容器使用电子邮件地址作为唯一键约束，并使用 `CompanyID` 作为分区键。 将用户的电子邮件地址配置为唯一键后，每个项将在给定的 `CompanyID` 中获得唯一的电子邮件地址。 无法创建具有重复电子邮件地址和相同分区键值的两个项。 

若要创建具有相同电子邮件地址但不是相同名字、姓氏和电子邮件地址的项，请将其他路径添加到唯一键策略。 还可以使用名字、姓氏和电子邮件的组合创建唯一键，而不是仅根据电子邮件地址创建唯一键。 此键称为复合唯一键。 在这种情况下，给定的 `CompanyID` 中允许这三个值的每种唯一组合。 

例如，容器可以包含具有以下值的项，其中每个项遵循唯一键约束。

| CompanyID | 名字 | 姓氏 |   电子邮件地址    |
|-----------|------------|-----------|--------------------|
|  Contoso  |    Gaby    |  Duperre  |  gaby@contoso.com  |
|  Contoso  |    Gaby    |  Duperre  | gaby@fabrikam.com  |
| Fabrikam  |    Gaby    |  Duperre  | gaby@fabrikam.com  |
| Fabrikam  |    Ivan    |  Duperre  | gaby@fabrikam.com  |
|  Fabrkam  |            |  Duperre  | gaby@fabraikam.com |
|  Fabrkam  |            |           | gaby@fabraikam.com |

如果你尝试使用上表中列出的组合插入另一个项，则会收到错误。 该错误指示不符合唯一键约束。 你会收到作为返回消息的 `Resource with specified ID or name already exists` 或 `Resource with specified ID, name, or unique index already exists`。 

## <a name="define-a-unique-key"></a>定义唯一键

只能在创建 Azure Cosmos 容器时定义唯一键。 唯一键的范围限定为逻辑分区。 在前面的示例中，如果基于邮政编码将容器分区，则每个逻辑分区中会出现重复项。 创建唯一键时请考虑以下属性：

* 不能将现有容器更新为使用不同的唯一键。 换而言之，使用唯一键策略创建容器后，无法更改策略。

* 若要为现有容器设置唯一键，请使用唯一键约束创建新容器。 使用适当的数据迁移工具将数据从现有容器移到新容器。 对于 SQL 容器，请使用[数据迁移工具](import-data.md)来移动数据。 对于 MongoDB 容器，请使用 [mongoimport.exe 或 mongorestore.exe](mongodb-migrate.md) 来移动数据。

* 一个唯一键策略最多可以包含 16 个路径值。 例如，值可以是 `/firstName`、`/lastName` 和 `/address/zipCode`。 每个唯一键策略可以具有最多 10 个唯一键约束或组合。 每个唯一索引约束的组合路径不得超过 60 字节。 在前面的示例中，名字、姓氏和电子邮件地址共同构成了一个约束。 此约束使用 16 个可能路径中的 3 个。

* 如果容器具有唯一键策略，则创建、更新和删除项时产生的[请求单位 (RU)](request-units.md) 费用要略高一些。

* 不支持稀疏的唯一键。 如果缺少某些唯一路径值，这些值将被视为 null 值，并参与唯一性约束。 因此，若要符合此约束，只能有一个项为 null 值。

* 唯一键名称区分大小写。 例如，假设某个容器的唯一键约束设置为 `/address/zipcode`。 如果数据包含名为 `ZipCode` 的字段，则 Azure Cosmos DB 会插入“null”作为唯一键，因为 `zipcode` 与 `ZipCode` 不同。 由于区分大小写，无法插入包含 ZipCode 的其他所有记录，因为重复的“null”违反唯一键约束。

## <a name="next-steps"></a>后续步骤

* 详细了解[逻辑分区](partition-data.md)。

<!-- Update_Description: update meta propreties, wording update -->