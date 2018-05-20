---
title: Azure API 管理的术语 | Microsoft Docs
description: 本文提供 API 管理特定的术语定义。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: 81cf34cacdfe37e25d6b745304ab0879245fd8da
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="terminology"></a>术语

本文提供 API 管理 (APIM) 特定的术语定义。

## <a name="term-definitions"></a>术语定义

* **后端 API** - 实现 API 的 HTTP 服务及其操作。 
* **前端 API**/**APIM API** - APIM API 不会托管 API，它只是创建 API 的结构，以根据需求自定义结构，而无需改动后端 API。 有关详细信息，请参阅[导入和发布 API](import-and-publish.md)。
* **APIM 产品** - 产品包含一个或多个 API 以及使用配额和使用条款。 可包含多个 API，并通过开发人员门户将其提供给开发人员。 有关详细信息，请参阅[创建和发布产品](api-management-howto-add-products.md)。
* **APIM API 操作** - 每个 API 表示一组可供开发人员使用的操作。 每个 APIM API 包含对实施 API 的后端服务的引用，而其操作映射到后端服务实施的操作。 有关详细信息，请参阅[模拟 API 响应](mock-api-responses.md)。
* **版本** - 有时，你希望将新的或不同的 API 功能发布给某些用户，还有一些人希望坚持使用目前他们所用的 API。 有关详细信息，请参阅[发布 API 的多个版本](api-management-get-started-publish-versions.md)。
* **修订版** - 当 API 准备就绪并即将供开发人员使用时，我们通常需要谨慎地对该 API 进行更改，同时避免干扰 API 的调用方。 另一种有效的做法是让开发人员知道所做的更改。 有关详细信息，请参阅[使用修订版](api-management-get-started-revise-api.md)。
* **开发人员门户** - 客户（开发人员）应使用开发人员门户来访问你的 API。 开发人员门户可自定义。 有关详细信息，请参阅[自定义开发人员门户](api-management-customize-styles.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建实例](get-started-create-service-instance.md)

