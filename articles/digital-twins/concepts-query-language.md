---
title: 查询语言
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生查询语言的基础知识。
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 29e1fa603600e246031f2a86aae3b0876b4910ba
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87562459"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>关于 Azure 数字孪生的查询语言

回忆一下，Azure 数字孪生中心是从**数字孪生**和**关系**构建的[**双子图形**](concepts-twins-graph.md)。 可以查询此关系图以获取有关它所包含的数字孪生和关系的信息。 这些查询使用类似于 SQL 的自定义查询语言编写，称为**Azure 数字孪生查询语言**。

若要从客户端应用将查询提交到服务，请使用 Azure 数字孪生[**查询 API**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)。 这使开发人员可以编写查询并应用筛选器来查找孪生中的数字集，以及有关 Azure 数字孪生方案的其他信息。

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>后续步骤

了解如何编写查询，以及[*如何在操作方法：查询双子图*](how-to-query-graph.md)中查看客户端代码示例。
