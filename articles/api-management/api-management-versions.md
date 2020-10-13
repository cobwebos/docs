---
title: Azure API 管理中的版本 |Microsoft Docs
description: 了解 Azure API 管理中版本的概念。
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 578bb511175d88a1507af9520265a1acd068b27c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095942"
---
# <a name="versions-in-azure-api-management"></a>Azure API 管理中的版本

版本允许向开发人员提供相关 Api 组。 你可以使用版本安全地处理 API 中的重大更改。 客户端可以选择在准备就绪时使用新的 API 版本，而现有客户端将继续使用较旧的版本。 版本通过版本标识符进行区分 (这是你选择) 的任何字符串值，而版本控制方案则允许客户端识别要使用的 API 版本。

在大多数情况下，可以将每个 API 版本视为自己的独立 API。 两个不同的 API 版本可能具有不同的操作集和不同的策略。

利用版本，你可以：

- 同时发布 API 的多个版本。
- 使用路径、查询字符串或标头来区分版本。
- 使用希望标识版本的任何字符串值，可以是数字、日期或名称。
- 在开发人员门户上显示组合在一起的 API 版本。
- 获取现有 (非版本化) API，并创建新版本的，而不会中断现有客户端。

[按照我们的演练操作开始使用版本。](./api-management-get-started-publish-versions.md)

## <a name="versioning-schemes"></a>版本控制方案

不同的 API 开发人员对版本控制具有不同的要求。 Azure API 管理不提供一种版本控制方法，而是提供多个选项。

### <a name="path-based-versioning"></a>基于路径的版本控制

使用路径版本控制方案时，版本标识符需要包含在任何 API 请求的 URL 路径中。

例如， `https://apis.contoso.com/products/v1` 和可 `https://apis.contoso.com/products/v2` 引用相同的 API， `products` 但可分别引用版本 `v1` 和版本 `v2` 。

使用基于标头的版本控制时 API 请求 URL 的格式为： `https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}` 。

### <a name="header-based-versioning"></a>基于标头的版本控制

如果使用了标头版本管理方案，则任何 API 请求的 HTTP 请求标头中都需要包含版本标识符。 可以指定 HTTP 请求标头的名称。

例如，你可以创建一个名为的自定义标头 `Api-Version` ，而客户端可以 `v1` `v2` 在此标头的值中指定或。

### <a name="query-string-based-versioning"></a>基于查询字符串的版本控制

使用查询字符串版本控制方案时，需要在任何 API 请求的查询字符串参数中包括版本标识符。 您可以指定查询字符串参数的名称。

使用基于查询字符串的版本控制时，API 请求 URL 的格式为： `https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}` 。

例如， `https://apis.contoso.com/products?api-version=v1` 和可 `https://apis.contoso.com/products/api-version=v2` 引用相同的 API， `products` 但可分别引用版本 `v1` 和版本 `v2` 。

## <a name="original-versions"></a>原始版本

如果将版本添加到非版本控制的 API，则会 `Original` 自动创建一个版本并在默认 URL 上响应，而无需指定版本标识符。 `Original`版本确保任何现有调用方都不会被添加版本的过程中断。 如果创建的新 API 在开始时启用了版本，则 `Original` 不会创建版本。

## <a name="how-versions-are-represented"></a>如何表示版本

Azure API 管理维护称为 *版本集*的资源，该资源表示用于单个逻辑 API 的一组版本。 当你使用 Azure 门户管理版本时，你看不到版本集，但是，如果你使用 PowerShell、资源管理器模板或 Azure 资源管理器 API 与 API 管理服务进行交互，则可以直接查看和管理版本集。 版本集包含受版本控制的 API 的显示名称，以及用于将请求定向到指定版本的 [版本控制方案](#versioning-schemes) 。

API 的每个版本都作为其自身的 API 资源进行维护，然后将其与版本集相关联。 版本集可能包含具有很多不同操作或策略的 Api，这反映了你可以在 API 版本之间进行重大更改。

### <a name="migrating-a-non-versioned-api-to-a-versioned-api"></a>将非版本控制的 API 迁移到版本控制的 API

当你使用 Azure 门户对现有 API 启用版本控制时，将对你的 API 管理资源进行以下更改：

 * 创建新版本集。
 * 现有版本保留并 [配置为 `Original` API 版本](#original-versions)。 API 链接到版本集，但是不需要指定版本标识符。
 * 新版本创建为新的 API，并链接到版本集。 必须使用版本控制方案和标识符访问此新 API。

## <a name="versions-and-revisions"></a>版本和修订版本

版本和修订版本都是不同的。 与非版本化的 API 一样，每个版本都可以有多个修订版本。 您可以使用修订版，而无需使用版本或其他方法。 通常，版本用于将 API 版本与重大更改分隔开来，而修订可用于对 API 进行少量和非重大更改。

如果你发现你的修订版本发生了重大更改，或者如果你想要将修订版正式转换为 beta/test 版本，则可以从修订版本创建版本。 使用 Azure 门户，单击 "修订" 选项卡上的修订上下文菜单中的 "从修订版创建版本"。

## <a name="developer-portal"></a>开发人员门户

[开发人员门户](./api-management-howto-developer-portal.md)分别列出了每个版本的 API。

![API 管理开发人员门户，其中显示了版本控制的 Api 列表](media/api-management-versions/portal-list.png)

API 的详细信息还显示了该 API 的所有版本的列表。 `Original`显示版本时没有版本标识符。

![API 管理开发人员门户，其中显示 API 的详细信息以及该 API 的版本列表](media/api-management-versions/portal-details.png)

> [!TIP]
> 需要将 API 版本添加到产品，然后才能在开发人员门户中看到它们。
