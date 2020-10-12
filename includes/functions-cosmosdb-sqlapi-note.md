---
title: include 文件
description: include 文件
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f7495c52e36bdc207145f17ec72eb57b7ebf1784
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "76279496"
---
Azure Cosmos DB 绑定只能与 SQL API 配合使用。 对于所有其他的 Azure Cosmos DB API，应使用适用于 API 的静态客户端通过函数来访问数据库。API 包括 [Azure Cosmos DB 的 API for MongoDB](../articles/cosmos-db/mongodb-introduction.md)、[Cassandra API](../articles/cosmos-db/cassandra-introduction.md)、[Gremlin API](../articles/cosmos-db/graph-introduction.md) 和[表 API](../articles/cosmos-db/table-introduction.md)。
