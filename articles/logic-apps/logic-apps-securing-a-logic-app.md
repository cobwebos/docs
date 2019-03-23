---
title: 保护对 Azure 逻辑应用的访问 | Microsoft 文档
description: 增加 Azure 逻辑应用的安全性，包括触发器、输入和输出、参数和其他服务
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.topic: article
ms.date: 02/05/2019
ms.openlocfilehash: 6f878e05255be8f65868d8d1dcdbacb3ccfa2729
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369932"
---
# <a name="secure-access-in-azure-logic-apps"></a>保护 Azure 逻辑应用中的访问

以下是逻辑应用中可以保护访问权限的元素：

* [请求或 Webhook 触发器](#secure-triggers)
* [管理、编辑或查看](#secure-operations)逻辑应用等操作
* 逻辑应用运行历史记录中的[输入和输出](#secure-run-history)
* [操作参数和输入](#secure-action-parameters)
* 从逻辑应用[获取请求的服务](#secure-requests)

<a name="secure-triggers"></a>

## <a name="secure-access-to-request-triggers"></a>保护对请求触发器的访问

当逻辑应用使用基于 HTTP 请求的触发器（如[请求](../connectors/connectors-native-reqres.md)或 [Webhook](../connectors/connectors-native-webhook.md) 触发器）时，你可以限制访问权限，以便只有经过授权的客户端才能启动逻辑应用。 逻辑应用接收到的所有请求都使用安全套接字层 (SSL) 协议进行加密和保护。 可使用不同的方法来保护对此触发器类型的访问：

* [生成共享访问签名](#sas)
* [限制传入 IP 地址](#restrict-incoming-ip-addresses)
* [添加 Azure Active Directory、OAuth 或其他安全标准](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures"></a>生成共享访问签名

逻辑应用的每个请求终结点在终结点的 URL 中都包含一个[共享访问签名 (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。 每个 URL 都包含 `sp`、`sv` 和 `sig` 查询参数：

* `sp` 指定映射到允许使用的 HTTP 方法的权限。
* `sv` 指定用于生成签名的版本。
* `sig` 用于对触发器的访问进行身份验证。

签名是使用 SHA256 算法生成的，所有 URL 路径和属性中都包含访问密钥。 该密钥永远不会公开或发布，而是一直处于加密状态并存储在逻辑应用中。 逻辑应用仅向那些包含有效签名（使用密钥创建）的触发器授权。 

以下是有关使用共享访问签名保护访问权限的详细信息：

* [重新生成访问密钥](#access-keys)
* [创建具有到期日期的回调 URL](#expiring-URLs)
* [使用主密钥或辅助密钥创建 URL](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>重新生成访问密钥

要随时重新生成新的安全访问密钥，请使用 Azure REST API 或 Azure 门户。 以前使用旧密钥生成的所有 URL 已失效，不再有权触发逻辑应用。 重新生成后检索的 URL 将使用新访问密钥进行签名。

1. 在 Azure 门户中，打开包含要重新生成密钥的逻辑应用。

1. 在逻辑应用菜单的“设置”下，选择“访问密钥”。

1. 选择要重新生成的密钥并完成生成过程。

<a name="expiring-urls"></a>

#### <a name="create-callback-urls-with-expiration-dates"></a>创建附带到期日期的回调 URL

如果与其他各方共享基于请求的触发器终结点的 URL，则可以根据需要生成具有特定密钥和到期日期的回调 URL。 然后，可以无缝地滚动密钥，或将触发逻辑应用的访问限制在特定的时间范围内。 可以使用[逻辑应用 REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers)为 URL 指定到期日期，例如：

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

在正文中，使用 JSON 日期字符串包含 `NotAfter` 属性。 该属性返回仅在 `NotAfter` 日期和时间之前有效的回调 URL。

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>创建附带主密钥或辅助密钥的 URL

生成或列出基于请求的触发器的回调 URL 时，还可以指定用于对 URL 进行签名的密钥。 可以使用[逻辑应用 REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) 生成由特定密钥签名的 URL，例如：

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

在正文中，包含值为 `Primary` 或 `Secondary` 的 `KeyType` 属性。 此属性返回由指定的安全密钥签名的 URL。

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>限制传入 IP 地址

除了共享访问签名外，还建议限制可以调用逻辑应用的特定客户端。  
例如，如果使用 Azure API 管理来管理请求终结点，则可以将逻辑应用限制为仅接受来自 API 管理实例的 IP 地址的请求。 

#### <a name="set-ip-ranges---azure-portal"></a>设置 IP 范围 - Azure 门户

要在 Azure 门户中设置此限制，请转到逻辑应用的设置： 

1. 在 Azure 门户的逻辑应用设计器中打开逻辑应用。 

1. 在逻辑应用的菜单中，在“设置”下，选择“工作流设置”。

1. 在“访问控制配置” > 
“允许的入站 IP 地址”下，请选择“特定 IP 范围”。

1. 在“触发器的 IP 范围”下，请指定触发器接受的 IP 地址范围。 有效的 IP 范围使用这些格式：x.x.x.x/x 或 x.x.x.x-x.x.x.x 

如果想让逻辑应用仅作为嵌套逻辑应用触发，请从“允许的入站 IP 地址”列表中选择“仅限其他逻辑应用”。 此选项会将空数组写入逻辑应用资源，因此只有来自逻辑应用服务（父级逻辑应用）的调用才能触发嵌套的逻辑应用。

> [!NOTE]
> 无论 IP 地址如何，仍可通过 Azure REST API 或 API 管理使用 `/triggers/{triggerName}/run` 来运行具有基于请求的触发器的逻辑应用。 不过，这种情况下仍需要针对 Azure REST API 进行身份验证，所有事件都会显示在 Azure 审核日志中。 请确保相应地设置访问控制策略。

#### <a name="set-ip-ranges---logic-app-deployment-template"></a>设置 IP 范围 - 逻辑应用的部署模板

如果使用 [Azure 资源管理器部署模板](../logic-apps/logic-apps-create-deploy-template.md)自动执行逻辑应用部署，则可以在该模板中设置 IP 范围，例如：

``` json
{
   "properties": {
      "definition": {},
      "parameters": {},
      "accessControl": {
         "triggers": {
            "allowedCallerIpAddresses": [
               {
                  "addressRange": "192.168.12.0/23"
               },
               {
                  "addressRange": "2001:0db8::/64"
               }
            ]
         }
      }
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>添加 Azure Active Directory、OAuth 或其他安全标准

要向逻辑应用添加更多授权协议，请考虑使用 [Azure API 管理](https://azure.microsoft.com/services/api-management/)。 此服务为所有终结点提供了丰富的监视信息、安全性、策略和文档，并且可以将逻辑应用公开为 API。 API 管理可以公开逻辑应用的公共或专用终结点，然后让其使用 Azure Active Directory、OAuth、证书或其他安全标准。 当 API 管理收到请求时，此服务会将请求发送到逻辑应用，同时也会进行任何必要的转换或限制。 要仅让 API 管理触发逻辑应用，可以使用逻辑应用的传入 IP 范围设置。 

<a name="secure-operations"></a>

## <a name="secure-access-to-logic-app-operations"></a>保护对逻辑应用操作的访问

要仅允许特定用户或组在逻辑应用上运行操作，可以限制对管理、编辑和查看等任务的访问权限。 逻辑应用支持 [Azure 基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-portal.md)，你可以为订阅中的成员自定义或分配内置角色，例如：

* **逻辑应用参与者**：用户可以查看、编辑和更新逻辑应用。 该角色无法删除逻辑应用或运行管理员操作。
* **逻辑应用操作员**：用户可以查看逻辑应用和运行历史记录，并启用或禁用逻辑应用。 该角色无法编辑或更新逻辑应用。

要防止他人更改或删除逻辑应用，可以使用 [Azure 资源锁](../azure-resource-manager/resource-group-lock-resources.md)。 此功能可以帮助防止他人更改或删除生产资源。

<a name="secure-run-history"></a>

## <a name="secure-access-to-logic-app-run-history"></a>保护对逻辑应用运行历史记录的访问

要保护之前逻辑应用运行中传递的输入或输出内容，可以限制为通过特定 IP 地址范围访问。 此功能提供了更多的访问控制。 在传输过程中和静态存储期间，逻辑应用运行中的所有数据都是加密的。 请求逻辑应用的运行历史记录时，逻辑应用对该请求进行身份验证，并提供指向逻辑应用工作流中来自请求和响应的输入和输出的链接。 可以保护这些链接，以便只有来自特定 IP 地址的请求才能返回该内容。 例如，甚至可以指定 IP 地址（例如 `0.0.0.0-0.0.0.0`），这样就没有人可以访问输入和输出。 只有拥有管理员权限的人员才能删除此限制，使“实时”访问逻辑应用的内容成为可能。

### <a name="set-ip-ranges---azure-portal"></a>设置 IP 范围 - Azure 门户

要在 Azure 门户中设置此限制，请转到逻辑应用的设置：

1. 在 Azure 门户的逻辑应用设计器中打开逻辑应用。 

1. 在逻辑应用的菜单中，在“设置”下，选择“工作流设置”。

1. 在“访问控制配置”> 
   “允许的入站 IP 地址”下，选择“特定 IP 范围”************。

1. 在“内容的 IP 范围”下，指定可以访问输入和输出中内容的 IP 地址范围。 
   有效的 IP 范围使用这些格式：x.x.x.x/x 或 x.x.x.x-x.x.x.x 

### <a name="set-ip-ranges---logic-app-deployment-template"></a>设置 IP 范围 - 逻辑应用的部署模板

如果使用 [Azure 资源管理器部署模板](../logic-apps/logic-apps-create-deploy-template.md)自动执行逻辑应用部署，则可以在该模板中设置 IP 范围，例如：

``` json
{
   "properties": {
      "definition": {},
      "parameters": {},
      "accessControl": {
         "contents": {
            "allowedCallerIpAddresses": [
               {
                  "addressRange": "192.168.12.0/23"
               },
               {
                  "addressRange": "2001:0db8::/64"
               }
            ]
         }
      }
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="secure-action-parameters"></a>

## <a name="secure-action-parameters-and-inputs"></a>保护操作参数和输入

在各种环境中进行部署时，建议对逻辑应用工作流定义中的特定元素进行参数化处理。 这样，你可以根据所使用的环境来提供输入并保护敏感信息。 例如，如果使用 [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication) 对 HTTP 操作进行身份验证，请定义并保护接受用于身份验证的客户端 ID 和客户端机密的参数。 对于这些参数，你的逻辑应用定义有其自己的 `parameters` 部分。
要在运行时访问参数值，可以使用[工作流定义语言](https://aka.ms/logicappsdocs)提供的 `@parameters('parameterName')` 表达式。 

若要保护在编辑逻辑应用或查看运行历史记录时不希望显示的参数和值，可以定义 `securestring` 类型的参数并根据需要使用编码。 具有此类型的参数不会随资源定义一起返回，并且在部署后无法通过查看资源来访问这些参数。

> [!NOTE]
> 如果在请求的标头或正文中使用参数，当访问逻辑应用的运行历史记录和传出的 HTTP 请求时，该参数可能是可见的。 请务必同时相应地设置内容访问策略。
> 始终不能通过输入或输出看见授权标头。 因此，如果在此处使用机密，则无法检索该机密。

有关在逻辑应用定义中保护参数的详细说明，请参阅本页下文中的[在逻辑应用定义中保护参数](#secure-parameters-workflow)。

如果使用 [Azure 资源管理器部署模板](../azure-resource-manager/resource-group-authoring-templates.md#parameters)自动执行部署，则还可以在这些模板中使用受保护的参数。 例如，在创建逻辑应用时，可以使用用于获取密钥保管库机密的参数。 部署模板定义具有其自己的 `parameters` 部分，这不同于逻辑应用的 `parameters` 部分。 有关在部署模板中保护参数的详细信息，请参阅本页下文中的[在部署模板中保护参数](#secure-parameters-deployment-template)。

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-logic-app-definitions"></a>在逻辑应用定义中保护参数

若要在逻辑应用工作流定义中保护敏感信息，请使用受保护的参数，以使该信息在保存逻辑应用后不可见。 例如，假设你在 HTTP 操作定义中使用 `Basic` 身份验证。 此示例包括一个 `parameters` 部分（它定义了操作定义的参数）和一个 `authentication` 部分（它接受 `username` 和 `password` 参数值）。 若要为这些参数提供值，可以使用一个单独的参数文件，例如：

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('usernameParam')",
               "password": "@parameters('passwordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "passwordParam": {
         "type": "securestring"
      },
      "userNameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

如果使用机密，则可以使用 [Azure 资源管理器密钥保管库](../azure-resource-manager/resource-manager-keyvault-parameter.md)在部署时获取这些机密。

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-deployment-templates"></a>在 Azure 资源管理器部署模板中保护参数

此示例显示了一个 Azure 资源管理器部署模板，该模板使用多个具有 `securestring` 类型的运行时参数：

* `armTemplatePasswordParam`，这是逻辑应用定义的 `logicAppWfParam` 参数的输入

* `logicAppWfParam`，这是使用基本身份验证的 HTTP 操作的输入

此示例包括一个内层 `parameters` 部分（该部分属于逻辑应用的工作流定义）和一个外层 `parameters` 部分（该部分属于部署模板）。 若要为参数指定环境值，可以使用一个单独的参数文件。 

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "logicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "armTemplatePasswordParam": {
         "type": "securestring"
      },
      "logicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('logicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('logicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('usernameParam')",
                           "password": "@parameters('logicAppWfParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "logicAppWfParam": {
                     "type": "securestring"
                  },
                  "userNameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "logicAppWfParam": {
                  "value": "[parameters('armTemplatePasswordParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

如果使用机密，则可以使用 [Azure 资源管理器密钥保管库](../azure-resource-manager/resource-manager-keyvault-parameter.md)在部署时获取这些机密。

<a name="secure-requests"></a>

## <a name="secure-access-to-services-receiving-requests"></a>保护对接收请求的服务的访问

通过以下方法可以保护逻辑应用需要访问和发送请求的任意终结点。

### <a name="add-authentication-on-outbound-requests"></a>针对出站请求添加身份验证

使用 HTTP、HTTP + Swagger（开放 API）或 Webhook 操作时，可以为逻辑应用发送的请求添加身份验证。 例如，可以使用基本身份验证、证书身份验证或 Azure Active Directory 身份验证。 有关详细信息，请参阅[对触发器或操作进行身份验证](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

### <a name="restrict-access-to-logic-app-ip-addresses"></a>限制对逻辑应用 IP 地址的访问

通过逻辑应用发出的所有调用都来自基于区域的特定指定 IP 地址。 可以添加仅接受来自这些 IP 地址的请求的筛选规则。 要了解这些 IP 地址，请参阅 [Azure 逻辑应用的限制和配置](logic-apps-limits-and-config.md#configuration)。

### <a name="secure-on-premises-connectivity"></a>保护本地连接性

Azure 逻辑应用可与这些服务集成，提供安全可靠的本地通信。

#### <a name="on-premises-data-gateway"></a>本地数据网关

Azure 逻辑应用的许多托管的连接器都可以安全连接到本地系统，例如，文件系统、SQL、SharePoint、DB2 等。 网关通过 Azure 服务总线发送来自加密通道上的本地源的数据。 所有流量最初都是网关代理的安全出站流量。 了解[本地数据网关的工作原理](logic-apps-gateway-install.md#gateway-cloud-service)。

#### <a name="azure-api-management"></a>Azure API 管理

[Azure API 管理](https://azure.microsoft.com/services/api-management/)提供本地连接选项，例如站点到站点虚拟专用网络和 ExpressRoute 集成，用于保护代理和与本地系统的通信。 在逻辑应用设计器中，可以从逻辑应用的工作流中选择 API 管理公开的 API，从而快速访问本地系统。

## <a name="next-steps"></a>后续步骤

* [创建部署模板](logic-apps-create-deploy-template.md)  
* [异常处理](logic-apps-exception-handling.md)  
* [监视逻辑应用](logic-apps-monitor-your-logic-apps.md)  
* [诊断逻辑应用的错误和问题](logic-apps-diagnosing-failures.md)  
