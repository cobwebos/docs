---
title: 在 Azure Cosmos DB 中使用唯一键
description: 了解如何使用 Azure Cosmos DB 数据库中的唯一键
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.reviewer: sngun
ms.openlocfilehash: 73d4ba0c82f26a6249528f2dbef1fd30f99ccedb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475867"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Azure Cosmos DB 中的唯一键约束

使用唯一键可在 Cosmos 容器中添加一个数据完整性层。 在创建 Cosmos 容器时创建唯一键策略。 使用唯一键可确保逻辑分区内一个或多个值的唯一性（可以保证每个[分区键](partition-data.md)的唯一性）。 使用唯一键策略创建容器后，该策略会根据唯一键约束的指定，阻止在逻辑分区中创建任何新的（或更新的）重复项。 分区键与唯一键的组合可保证某个项在容器范围内的唯一性。

例如，假设某个 Cosmos 容器使用电子邮件地址作为唯一键约束，并使用 `CompanyID` 作为分区键。 通过将用户的电子邮件地址配置为唯一键，可确保每个项在给定的 `CompanyID` 中具有唯一的电子邮件地址。 无法创建具有重复电子邮件地址和相同分区键值的两个项。  

若要使用户能够创建多项具有相同电子邮件地址的项，但名字、姓氏和电子邮件地址不同，则可以将其他路径添加到唯一键策略。 如果不基于电子邮件地址创建唯一键，还可以使用名字、姓氏和电子邮件的组合创建唯一键（复合唯一键）。 在这种情况下，给定的 `CompanyID` 中允许这三个值的每种唯一组合。 例如，容器可以包含具有以下值的项，其中每个项遵循唯一键约束。

|CompanyID|名字|姓氏|电子邮件地址|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

如果你尝试使用上表中列出的组合插入另一个项，则会收到一个错误，指示不符合唯一键约束。 返回消息中会显示“具有指定 ID 或名称的资源已存在”或“具有指定 ID、名称或唯一索引的资源已存在”。  

## <a name="defining-a-unique-key"></a>定义唯一键

只能在创建 Cosmos 容器时定义唯一键。 唯一键的范围限定为逻辑分区。 在前面的示例中，如果基于邮政编码将容器分区，则每个逻辑分区中会出现重复项。 创建唯一键时请考虑以下属性：

* 不能将现有容器更新为使用不同的唯一键。 换而言之，一旦使用唯一键策略创建了容器，就无法更改策略。

* 若要为现有容器设置唯一键，必须使用唯一键约束创建新容器，并使用适当的数据迁移工具将数据从现有容器移到新容器。 对于 SQL 容器，请使用[数据迁移工具](import-data.md)来移动数据。 对于 MongoDB 容器，请使用 [mongoimport.exe 或 mongorestore.exe](mongodb-migrate.md) 来移动数据。

* 一个唯一键策略最多可以包含 16 个路径值（例如 /firstName、/lastName、/address/zipCode）。 每个唯一键策略最多可以包含 10 个唯一键约束或组合，每个唯一索引约束的组合路径不应超过 60 字节。 在前面的示例中，名字、姓氏和电子邮件地址共同构成了一个约束，它使用 16 个可能路径中的 3 个。

* 如果容器具有唯一键策略，则创建、更新和删除项时产生的请求单位 (RU) 费用要略高一些。

* 不支持稀疏的唯一键。 如果缺少某些唯一路径值，这些值将被视为 null 值，并参与唯一性约束。 因此，若要符合此约束，只能有一个项为 null 值。

* 唯一键名称区分大小写。 例如，假设某个容器的唯一键约束设置为 /address/zipcode。 如果数据包含名为 ZipCode 的字段，则 Cosmos DB 会插入“null”作为唯一键，因为“zipcode”与“ZipCode”不同。 由于区分大小写，无法插入包含 ZipCode 的其他所有记录，因为重复的“null”违反唯一键约束。

## <a name="next-steps"></a>后续步骤

* 详细了解[逻辑分区](partition-data.md)
