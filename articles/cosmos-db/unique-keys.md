---
title: 在 Azure Cosmos DB 中使用唯一键
description: 了解如何使用 Azure Cosmos 数据库中的唯一键
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.reviewer: sngun
ms.openlocfilehash: 3c5e8a2c85898175772dc353258e77fc8e0a74f2
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263224"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Azure Cosmos DB 中的唯一键约束

唯一键在 Azure Cosmos 容器中添加一个数据完整性层。 在创建 Azure Cosmos 容器时将创建唯一键策略。 使用唯一键可确保逻辑分区内一个或多个值的唯一性。 此外，可以保证每个[分区键](partition-data.md)的唯一性。 

使用唯一键策略创建容器后，可以防止创建新的或现有项导致的逻辑分区内重复的更新，所指定的唯一键约束。 分区键与唯一键的组合可保证某个项在容器范围内的唯一性。

例如，假设某个 Azure Cosmos 容器使用电子邮件地址作为唯一键约束，并使用 `CompanyID` 作为分区键。 将用户的电子邮件地址配置为唯一键后，每个项将在给定的 `CompanyID` 中获得唯一的电子邮件地址。 无法创建具有重复电子邮件地址和相同分区键值的两个项。 

若要创建具有相同电子邮件地址但不是相同名字、姓氏和电子邮件地址的项，请将其他路径添加到唯一键策略。 而不是创建基于电子邮件地址仅一个独一无二的密钥，还可以创建唯一键的第一个名称组合，姓氏、 和电子邮件地址。 此键称为复合唯一键。 在这种情况下，给定的 `CompanyID` 中允许这三个值的每种唯一组合。 

例如，容器可以包含具有以下值的项，其中每个项遵循唯一键约束。

|CompanyID|名字|姓氏|电子邮件地址|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

如果你尝试使用上表中列出的组合插入另一个项，则会收到错误。 该错误指示不符合唯一键约束。 您收到`Resource with specified ID or name already exists`或`Resource with specified ID, name, or unique index already exists`作为返回的消息。 

## <a name="define-a-unique-key"></a>定义唯一键

只能在创建 Azure Cosmos 容器时定义唯一键。 唯一键的范围限定为逻辑分区。 在前面的示例中，如果基于邮政编码将容器分区，则每个逻辑分区中会出现重复项。 创建唯一键时请考虑以下属性：

* 不能将现有容器更新为使用不同的唯一键。 换而言之，使用唯一键策略创建容器后，无法更改策略。

* 若要为现有容器设置唯一键，请使用唯一键约束创建新容器。 使用适当的数据迁移工具将数据从现有容器移到新容器。 对于 SQL 容器，请使用[数据迁移工具](import-data.md)来移动数据。 对于 MongoDB 容器，请使用 [mongoimport.exe 或 mongorestore.exe](mongodb-migrate.md) 来移动数据。

* 一个唯一键策略最多可以包含 16 个路径值。 例如，值可能`/firstName`， `/lastName`，和`/address/zipCode`。 每个唯一键策略可以具有最多 10 个唯一键约束或组合。 每个唯一索引约束的组合路径不得超过 60 字节。 在前面的示例中，名字、姓氏和电子邮件地址共同构成了一个约束。 此约束使用 16 个可能路径中的 3 个。

* 当容器具有唯一键策略，[请求单位 (RU)](request-units.md)费用来创建、 更新和删除项会略高。

* 不支持稀疏的唯一键。 如果缺少某些唯一路径值，这些值将被视为 null 值，并参与唯一性约束。 因此，若要符合此约束，只能有一个项为 null 值。

* 唯一键名称区分大小写。 例如，考虑具有设置为的唯一键约束的容器`/address/zipcode`。 如果您的数据具有一个名为字段`ZipCode`，Azure Cosmos DB 将插入作为唯一键"null"因为`zipcode`未与相同`ZipCode`。 由于区分大小写，无法插入包含 ZipCode 的其他所有记录，因为重复的“null”违反唯一键约束。

## <a name="next-steps"></a>后续步骤

* 详细了解[逻辑分区](partition-data.md)。
