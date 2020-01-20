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
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279496"
---
Azure Cosmos DB 绑定只能与 SQL API 配合使用。 对于所有其他的 Azure Cosmos DB API，应使用适用于 API 的静态客户端通过函数来访问数据库。API 包括 [Azure Cosmos DB 的 API for MongoDB](../articles/cosmos-db/mongodb-introduction.md)、[Cassandra API](../articles/cosmos-db/cassandra-introduction.md)、[Gremlin API](../articles/cosmos-db/graph-introduction.md) 和[表 API](../articles/cosmos-db/table-introduction.md)。
