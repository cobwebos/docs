---
title: Azure Cosmos DB 中的跨源资源共享 (CORS)
description: 本文介绍如何使用 Azure 门户和 Azure 资源管理器模板配置 Azure Cosmos DB 中的跨源资源共享 (CORS)。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2018
ms.author: dech
ms.openlocfilehash: 689b0bb08e300efc7c5e93dbf346040b82a7c52d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61059753"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>配置跨源资源共享 (CORS) 

跨源资源共享 (CORS) 是一项 HTTP 功能，使在一个域中运行的 Web 应用程序能够访问另一个域中的资源。 Web 浏览器实施一种称为“同源策略”的安全限制，防止网页调用不同域中的 API。 但是，CORS 提供了一种安全的方法，允许源域调用其他域中的 API。Azure Cosmos DB 中的核心 SQL API 现在通过使用“allowedOrigins”标头支持跨源资源共享 (CORS)。 为 Azure Cosmos 帐户启用 CORS 支持后，仅对经过身份验证的请求进行评估，以根据指定的规则确定是否允许这些请求。

可以使用 Azure 门户或 Azure 资源管理器模板配置跨源资源共享 (CORS) 设置。 Azure Cosmos DB 中的核心 SQL API 支持同时适用于 Node.js 和基于浏览器的环境的 JavaScript 库。 使用网关模式时，该库现在可以充分利用 CORS 支持。 无需客户端配置即可使用此功能。 借助 CORS 支持，来自浏览器的资源可以通过 [JavaScript 库](https://www.npmjs.com/package/@azure/cosmos)直接访问 Azure Cosmos DB 或直接通过 [REST API](https://docs.microsoft.com/rest/api/cosmos-db/) 进行访问来执行简单操作。 

## <a name="enable-cors-support-from-azure-portal"></a>通过 Azure 门户启用 CORS 支持

使用以下步骤通过 Azure 门户启用跨源资源共享：

1. 导航到 Azure Cosmos DB 帐户。 打开“CORS”边栏选项卡。

2. 指定可以对 Azure Cosmos DB 帐户执行跨源调用的源的逗号分隔列表。 例如：`https://www.mydomain.com`、`https://mydomain.com`、`https://api.mydomain.com`。 还可以使用通配符“\*”允许所有源，然后选择“提交”。 

   > [!NOTE]
   > 目前，不能将通配符用作域名的一部分。 例如，尚不支持 `https://*.mydomain.net` 格式。 
   
   ![使用 Azure 门户启用跨源资源共享](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)
 
## <a name="enable-cors-support-from-resource-manager-template"></a>使用资源管理器模板启用 CORS 支持

若要使用资源管理器模板启用 CORS，请将具有“allowedOrigins”属性的“cors”部分添加到任何现有模板。 以下 JSON 是用于创建启用了 CORS 的新 Azure Cosmos 帐户的模板的示例。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "name": "test",
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "apiVersion": "2015-04-08",
            "location": "East US 2",
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "Session",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "test-eastus2",
                        "failoverPriority": 0,
                        "locationName": "East US 2"
                    }
                ],
                "cors": [
                    {
                        "allowedOrigins": "*"
                    }
                ]
            },
            "dependsOn": [
            ]
        }
    ]
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>从浏览器使用 Azure Cosmos DB JavaScript 库

目前，Azure Cosmos DB JavaScript 库只有 CommonJS 版本的库（随附其包）。 若要从浏览器使用此库，必须使用 Rollup 或 Webpack 等工具来创建与浏览器兼容的库。 某些 Node.js 库应具有它们的浏览器模拟。 下面是具有所需模拟设置的 webpack 配置文件的示例。

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
下面是将 TypeScript 和 Webpack 与 Azure Cosmos DB JavaScript SDK 库配合使用来生成创建新项时发送实时更新的 Todo 应用的[代码示例](https://github.com/christopheranderson/cosmos-browser-sample)。
最佳做法是，不要使用主键从浏览器与 Azure Cosmos DB 进行通信。 而应使用资源令牌进行通信。 有关资源令牌的详细信息，请参阅[保护对 Azure Cosmos DB 的访问](secure-access-to-data.md#resource-tokens)一文。

## <a name="next-steps"></a>后续步骤

若要了解有关保护 Azure Cosmos 帐户的其他方式，请参阅以下文章：

* [为 Azure Cosmos DB 配置防火墙](how-to-configure-firewall.md)一文。

* [为 Azure Cosmos DB 帐户配置基于虚拟网络和子网的访问](how-to-configure-vnet-service-endpoint.md)
    

