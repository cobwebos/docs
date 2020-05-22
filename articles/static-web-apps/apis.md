---
title: Azure 静态 Web 应用中通过 Azure Functions 提供 API 支持
description: 了解 Azure 静态 Web 应用支持的 API 功能
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 7f86ce9158b5b07b036c785c2973e8a5883ed686
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594266"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>Azure 静态 Web 应用预览中通过 Azure Functions 提供 API 支持

Azure 静态 Web 应用通过 [Azure Functions](../azure-functions/functions-overview.md) 提供无服务器 API 终结点。 利用 Azure Functions，API 根据需要动态缩放，并包括以下功能：

- 集成安全性，可直接访问用户[身份验证和基于角色的授权](user-information.md)数据。
- 无缝路由，可向 Web 应用安全地提供 api 路由，而无需自定义 CORS 规则。
- Azure Functions v3 与 Node.js 12 兼容。
- HTTP 触发器和输出绑定。

## <a name="configuration"></a>配置

API 终结点通过 api 路由向 Web 应用提供。 虽然此路由已固定，但你可以控制在其中找到关联的 Azure Functions 应用的文件夹。 可以通过[编辑工作流 YAML 文件](github-actions-workflow.md#build-and-deploy)来改变此位置，该文件位于存储库的 .github/workflows 文件夹中的。

## <a name="constraints"></a>约束

Azure 静态 Web 应用通过 Azure Functions 提供 API。 Azure Functions 的功能侧重于一组特定的功能，这些功能可为 Web 应用创建 API，并允许 Web 应用安全地连接到 API。 这些功能会有一些限制，包括：

- API 路由前缀必须是 api。
- 触发器和绑定仅限于 [HTTP](../azure-functions/functions-bindings-http-webhook.md)。
  - 除输出绑定外，所有其他 [Azure Functions 触发器和绑定](../azure-functions/functions-triggers-bindings.md#supported-bindings)都受到限制。
- 仅当将 [Application Insights](../azure-functions/functions-monitoring.md) 添加到 Functions 应用中时，才能使用日志。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加 API](add-api.md)
