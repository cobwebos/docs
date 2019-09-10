---
title: 保护访问和数据 - Azure 逻辑应用
description: 保护参数输入、HTTP 请求触发器、运行历史记录、逻辑应用操作，以及与 Azure 逻辑应用中的其他服务建立的连接
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: 6c16b38cce31c45158a5871c10dbd01339da9203
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845433"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>在 Azure 逻辑应用中保护访问和数据

若要在 Azure 逻辑应用中控制访问和保护数据，可在以下方面设置安全性：

* [对 HTTP 请求触发器的访问](#secure-triggers)
* [对逻辑应用操作的访问](#secure-operations)
* [对运行历史记录输入和输出的访问](#secure-run-history)
* [对参数输入的访问](#secure-action-parameters)
* [对从逻辑应用调用的服务和系统的访问](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-http-request-triggers"></a>对 HTTP 请求触发器的访问

当逻辑应用使用基于 HTTP 请求的触发器（如[请求](../connectors/connectors-native-reqres.md)或 [Webhook](../connectors/connectors-native-webhook.md) 触发器）时，你可以限制访问权限，以便只有经过授权的客户端才能启动逻辑应用。 逻辑应用接收到的所有请求都使用安全套接字层 (SSL) 协议进行加密和保护。 

可使用以下方法来保护对此触发器类型的访问：

* [生成共享访问签名](#sas)
* [限制传入 IP 地址](#restrict-incoming-ip-addresses)
* [添加 Azure Active Directory、OAuth 或其他安全标准](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>生成共享访问签名 (SAS)

逻辑应用上的每个请求终结点在终结点的 URL 中都包含一个[共享访问签名 (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas)，其格式如下：

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

每个 URL 包含下表中所述的 `sp`、`sv` 和 `sig` 查询参数：

| 查询参数 | 描述 |
|-----------------|-------------|
| `sp` | 指定允许的 HTTP 方法使用的权限。 |
| `sv` | 指定用于生成签名的 SAS 版本。 |
| `sig` | 指定用于对触发器访问进行身份验证的签名。 此签名是使用 SHA256 算法生成的，所有 URL 路径和属性中都包含机密访问密钥。 该密钥永远不会公开或发布，而是一直处于加密状态并存储在逻辑应用中。 逻辑应用仅向那些包含有效签名（使用密钥创建）的触发器授权。 |
|||

有关使用共享访问签名保护访问的详细信息，请参阅本主题中的以下部分：

* [重新生成访问密钥](#access-keys)
* [创建具有到期日期的回调 URL](#expiring-urls)
* [使用主密钥或辅助密钥创建 URL](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>重新生成访问密钥

若要随时重新生成新的安全访问密钥，请使用 Azure REST API 或 Azure 门户。 以前使用旧密钥生成的所有 URL 已失效，不再有权触发逻辑应用。 重新生成后检索的 URL 将使用新访问密钥进行签名。

1. 在 Azure 门户中，打开包含要重新生成密钥的逻辑应用。

1. 在逻辑应用菜单的“设置”下，选择“访问密钥”。

1. 选择要重新生成的密钥并完成生成过程。

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>创建具有过期日期的回调 URL

如果与其他各方共享基于 HTTP 请求的触发器的终结点 URL，可以生成使用特定密钥并具有过期日期的回调 URL。 这样，就可以无缝滚动更新密钥，或者根据特定的时间跨度将触发逻辑应用的访问限制。 若要为 URL 指定过期日期，请使用[逻辑应用 REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers)，例如：

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在正文中，使用 JSON 日期字符串包含 `NotAfter` 属性。 该属性返回仅在 `NotAfter` 日期和时间之前有效的回调 URL。

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>创建附带主密钥或辅助密钥的 URL

生成或列出基于 HTTP 请求的触发器的回调 URL 时，可以指定用于对 URL 进行签名的密钥。 若要生成由特定密钥签名的 URL，请使用[逻辑应用 REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers)，例如：

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在正文中，包含值为 `Primary` 或 `Secondary` 的 `KeyType` 属性。 此属性返回由指定的安全密钥签名的 URL。

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>限制传入 IP 地址

除了共享访问签名外，还建议限制可以调用逻辑应用的特定客户端。 例如，如果使用 Azure API 管理来管理请求终结点，则可以将逻辑应用限制为仅接受来自 API 管理实例的 IP 地址的请求。

#### <a name="restrict-incoming-ip-ranges-in-azure-portal"></a>在 Azure 门户中限制传入的 IP 范围

1. 在 Azure 门户的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用的菜单中，在“设置”下，选择“工作流设置”。

1. 在“访问控制配置” > “允许的入站 IP 地址”下，请选择“特定 IP 范围”。

1. 在“触发器的 IP 范围”下，请指定触发器接受的 IP 地址范围。

   有效的 IP 范围使用这些格式：x.x.x.x/x 或 x.x.x.x-x.x.x.x

如果想让逻辑应用仅作为嵌套逻辑应用触发，请从“允许的入站 IP 地址”列表中选择“仅限其他逻辑应用”。 此选项会将空数组写入逻辑应用资源。 这样，只有来自逻辑应用服务（父级逻辑应用）的调用才能触发嵌套的逻辑应用。

> [!NOTE]
> 无论 IP 地址如何，仍可通过 Azure REST API 或 API 管理使用 `/triggers/<trigger-name>/run` 来运行具有基于 HTTP 请求的触发器的逻辑应用。 不过，这种情况下仍需要针对 Azure REST API 进行身份验证。 所有事件将显示在 Azure 审核日志中。 请确保相应地设置访问控制策略。

#### <a name="restrict-incoming-ip-ranges-in-azure-resource-manager-template"></a>在 Azure 资源管理器模板中限制传入的 IP 范围

如果使用 [Azure 资源管理器模板](../logic-apps/logic-apps-create-deploy-template.md)自动化逻辑应用部署，可以在逻辑应用的资源定义中使用 `accessControl` 节和 `triggers` 节来指定 IP 范围，例如：

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
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
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>添加 Azure Active Directory、OAuth 或其他安全标准

要向逻辑应用添加更多授权协议，请考虑使用 [Azure API 管理](../api-management/api-management-key-concepts.md)。 此服务可让你将逻辑应用公开为 API，并为所有终结点提供了丰富的监视信息、安全性、策略和文档。 API 管理可以公开逻辑应用的公共或专用终结点，然后让其使用 Azure Active Directory、OAuth、证书或其他安全标准。 当 API 管理收到请求时，此服务会将请求发送到逻辑应用，同时也会进行任何必要的转换或限制。 要仅让 API 管理触发逻辑应用，可以使用逻辑应用的传入 IP 范围设置。

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>对逻辑应用操作的访问

可以仅允许特定的用户或组运行特定的操作，例如管理、编辑和查看逻辑应用。 若要控制其权限，请使用 [Azure 基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-portal.md) 为订阅中的成员分配自定义角色或内置角色：

* [逻辑应用参与者](../role-based-access-control/built-in-roles.md#logic-app-contributor)：允许管理逻辑应用，但不允许更改其访问权限。

* [逻辑应用操作员](../role-based-access-control/built-in-roles.md#logic-app-operator)：允许读取、启用和禁用逻辑应用，但不允许编辑或更新它们。

若要防止他人更改或删除你的逻辑应用，可以使用 [Azure 资源锁](../azure-resource-manager/resource-group-lock-resources.md)，它可以防止他人更改或删除生产资源。

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>对运行历史记录数据的访问

在逻辑应用运行期间，在传输过程中使用[传输层安全性（TLS）](https://azure.microsoft.com/updates/app-service-and-functions-hosted-apps-can-now-update-tls-versions/)和[静态](../security/fundamentals/encryption-atrest.md)加密所有数据。 逻辑应用完成运行后，你可以查看该运行的历史记录，包括运行的步骤以及每个操作的状态、持续时间、输入和输出。 这些丰富的详细信息可让你深入了解逻辑应用的运行方式，以及可以从何处开始排查出现的任何问题。

访问逻辑应用的运行历史记录时，逻辑应用将对该访问进行身份验证，并提供指向逻辑应用的运行中来自请求和响应的输入和输出的链接。 但是，对于处理任何密码、机密、密钥或其他敏感信息的操作，你需要阻止他人查看和访问该数据。 例如，如果逻辑应用从 [Azure Key Vault](../key-vault/key-vault-whatis.md) 获取机密，以便在对 HTTP 操作进行身份验证时使用，则你需要在视图中隐藏该机密。

若要控制对逻辑应用运行历史记录中的输入和输出的访问，可使用以下选项：

* [按 IP 地址范围限制访问](#restrict-ip)。

  使用此选项可以根据来自特定 IP 地址范围的请求保护对运行历史记录的访问。

* [使用混淆向运行历史记录隐藏数据](#obfuscate)。

  在许多触发器和操作中，可以向逻辑应用的运行历史记录隐藏其输入和/或输出。

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>按 IP 地址范围限制访问

可以限制对逻辑应用运行历史记录中的输入和输出的访问，以便只有来自特定 IP 地址范围的请求才能查看这些数据。 例如，若要阻止任何人访问输入和输出，请指定类似于 `0.0.0.0-0.0.0.0` 的 IP 地址范围。 只有拥有管理员权限的人员才能去除此限制，使“实时”访问逻辑应用的数据成为可能。 可以使用 Azure 门户或者在用于逻辑应用部署的 Azure 资源管理器模板中指定要限制的 IP 范围。

#### <a name="restrict-ip-ranges-in-azure-portal"></a>在 Azure 门户中限制 IP 范围

1. 在 Azure 门户的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用的菜单中，在“设置”下，选择“工作流设置”。

1. 在“访问控制配置” > “允许的入站 IP 地址”下，请选择“特定 IP 范围”。

1. 在“内容的 IP 范围”下，指定可以访问输入和输出中内容的 IP 地址范围。 

   有效的 IP 范围使用这些格式：x.x.x.x/x 或 x.x.x.x-x.x.x.x

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>在 Azure 资源管理器模板中限制 IP 范围

如果使用 [Azure 资源管理器模板](../logic-apps/logic-apps-create-deploy-template.md)自动化逻辑应用部署，可以在逻辑应用的资源定义中使用 `accessControl` 节和 `contents` 节来指定 IP 范围，例如：

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
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
         }
      }
   ],
   "outputs": {}
}
```

<a name="obfuscate"></a>

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>使用混淆向运行历史记录隐藏数据

许多触发器和操作都有可向逻辑应用的运行历史记录隐藏输入和/或输出的设置。 以下是在使用这些设置保护此数据时[需要查看的一些注意事项](#obfuscation-considerations)。

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>在设计器中保护输入和输出

1. 如果尚未在 [Azure 门户](https://portal.azure.com)中打开你的逻辑应用，请在逻辑应用设计器中将其打开。

   ![打开示例逻辑应用](media/logic-apps-securing-a-logic-app/sample-logic-app.png)

1. 对于要保护其数据的触发器或操作，请选择省略号 ( **...** ) 按钮，然后选择“设置”。

   ![打开“设置”](media/logic-apps-securing-a-logic-app/open-settings.png)

1. 启用“保护输入”和/或“保护输出”。 完成后，选择“完成”。

   ![启用“保护输入”或“保护输出”](media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   现在，该操作或触发器将在标题栏中显示一个锁形图标。

   ![标题栏上的锁形图标](media/logic-apps-securing-a-logic-app/title-bar-lock-icon.png)

   表示上述操作中受保护输出的标记也会显示锁形图标。 例如，从动态内容列表中选择要在某个操作中使用的此类输出时，该标记将显示一个锁形图标。

   ![选择输出](media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. 逻辑应用运行后，你可以查看该运行的历史记录。

   1. 在逻辑应用的”概述“窗格中，选择要查看的运行。

   1. 在“逻辑应用运行”窗格中，展开要查看的操作。

      如果已选择同时保护输入和输出，则这些值现在会隐藏。

      ![已在运行历史记录中隐藏数据](media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>在代码视图中保护输入和输出

在基础触发器或操作定义中，使用以下两个值中的一个或两个值添加或更新 `runtimeConfiguration.secureData.properties` 数组：

* `"inputs"`：在运行历史记录中保护输入。
* `"outputs"`：在运行历史记录中保护输出。

以下是在使用这些设置保护此数据时[需要查看的一些注意事项](#obfuscation-considerations)。

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>隐藏输入和输出时的注意事项

* 保护触发器或操作的输入或输出时，逻辑应用不会将受保护的数据发送到 Azure Log Analytics。 此外，你无法将[跟踪的属性](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details)添加到该触发器或操作进行监视。

* [用于处理工作流历史记录的逻辑应用 API](https://docs.microsoft.com/rest/api/logic/) 不会返回受保护的输出。

* 在输入受保护的操作或显式使用受保护的输出的操作中，如果此操作返回的响应具有包含此受保护数据的输出，则必须在此操作中手动启用“保护输出”才能保护这些输出。

* 在预期运行历史记录会保护其中的该数据的下游操作中，请务必启用“保护输入”或“保护输出”。

  **“保护输出”设置**

  在触发器或操作中手动启用“保护输出”时，逻辑应用将在运行历史记录中保护这些输出。 如果下游操作显式使用这些受保护的输出作为输入，则逻辑应用会在运行历史记录中隐藏此操作的输入，但不会启用该操作的“保护输入”设置。

  ![用作输入的受保护输出，以及对大多数操作的下游影响](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  “撰写”、“分析 JSON”和“响应”操作仅提供“保护输入”设置。 启用后，此设置也会隐藏这些操作的输出。 如果这些操作显式使用上游的受保护输出作为输入，则逻辑应用会隐藏这些操作的输入和输出，但不会启用这些操作的“保护输入”设置。 如果下游操作显式使用“撰写”、“分析 JSON”或“响应”操作中隐藏的输出作为输入，则逻辑应用不会隐藏此下游操作的输入或输出。

  ![用作输入的受保护输出，以及对特定操作的下游影响](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **“保护输入”设置**

  在触发器或操作中手动启用“保护输入”时，逻辑应用将在运行历史记录中保护这些输入。 如果下游操作显式使用该触发器或操作中的可见输出作为输入，则逻辑应用将在运行历史记录中隐藏此下游操作的输入，但不会在此操作中启用“保护输入”，且不会隐藏此操作的输出。

  ![受保护的输入以及对大多数操作的下游影响](media/logic-apps-securing-a-logic-app/secure-inputs-flow.png)

  如果“撰写”、“分析 JSON”和“响应”操作显式使用具有受保护输入的触发器或操作中的可见输出，则逻辑应用将隐藏这些操作的输入和输出，但不会启用这些操作的“保护输入”设置。 如果下游操作显式使用“撰写”、“分析 JSON”或“响应”操作中隐藏的输出作为输入，则逻辑应用不会隐藏此下游操作的输入或输出。

  ![受保护的输入以及对特定操作的下游影响](media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>对参数输入的访问

如果在不同的环境中进行部署，请考虑将工作流定义中的值（根据环境而异）参数化。 这样，便可以使用 [Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md#parameters)来部署逻辑应用，通过定义受保护的参数来保护敏感信息，并使用[参数文件](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values)通过模板的参数单独提供这些参数输入。

例如，如果使用 [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication) 对 HTTP 操作进行身份验证，则可以定义并保护接受用于身份验证的客户端 ID 和客户端机密的参数。 若要为逻辑应用定义这些参数，请使用逻辑应用工作流定义中的 `parameters` 节。 若要保护在编辑逻辑应用或查看运行历史记录时不希望显示的参数值，请使用 `securestring` 或 `secureobject` 类型并根据需要使用编码来定义参数。 具有此类型的参数不会随资源定义一起返回，并且在部署后无法通过查看资源来访问这些参数。 若要在运行时访问这些参数值，请在工作流定义中使用 `@parameters('<parameter-name>')` 表达式。 此表达式仅在运行时计算，由[工作流定义语言](../logic-apps/logic-apps-workflow-definition-language.md)描述。

> [!NOTE]
> 如果在 HTTP 请求的标头或正文中使用参数，查看逻辑应用的运行历史记录和传出的 HTTP 请求时，该参数可能是可见的。 另外，请务必相应地设置内容访问策略。 始终不能通过输入或输出看见授权标头。 因此，如果在此处使用机密，则无法检索该机密。

有关详细信息，请参阅本主题稍后的[保护工作流定义中的参数](#secure-parameters-workflow)。

使用 [Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md#parameters)自动化部署时，可以使用 `securestring` 和 `secureobject` 类型定义在部署时计算的受保护模板参数。 若要定义模板参数，请使用模板的顶级 `parameters` 节，该节不同于工作流定义的 `parameters` 节。 若要提供模板参数的值，请使用单独的[参数文件](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values)。

例如，如果使用机密，则可以定义并使用可在部署时从 [Azure Key Vault](../key-vault/key-vault-whatis.md) 检索这些机密的受保护模板参数。 然后，可以在参数文件中引用 Key Vault 和机密。 有关详细信息，请参阅以下主题：

* [在部署时使用 Azure Key Vault 传递安全参数值](../azure-resource-manager/resource-manager-keyvault-parameter.md)
* 本主题稍后的[保护 Azure 资源管理器模板中的参数](#secure-parameters-deployment-template)。

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>保护工作流定义中的参数

若要在逻辑应用工作流定义中保护敏感信息，请使用受保护的参数，以使该信息在保存逻辑应用后不可见。 例如，假设某个 HTTP 操作需要使用用户名和密码进行基本身份验证。 在工作流定义中，`parameters` 节使用 `securestring` 类型定义 `basicAuthPasswordParam` 和 `basicAuthUsernameParam` 参数。 然后，操作定义将引用 `authentication` 节中的这些参数。

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
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
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

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>保护 Azure 资源管理器模板中的参数

逻辑应用的资源管理器模板包含多个 `parameters` 节。 若要保护密码、密钥、机密和其他敏感信息，请使用 `securestring` 或 `secureobject` 类型在模板级别和工作流定义级别定义受保护参数。 然后，可将这些值存储在 [Azure Key Vault](../key-vault/key-vault-whatis.md) 中，并使用[参数文件](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values)来引用 Key Vault 和机密。 模板在部署时将检索该信息。 有关详细信息，请参阅[在部署时使用 Azure Key Vault 传递安全参数值](../azure-resource-manager/resource-manager-keyvault-parameter.md)。

下面是有关这些 `parameters` 节的详细信息：

* 在模板的最高级别，`parameters` 节定义了模板在部署时使用的值的参数。 例如，这些值可能包含特定部署环境的连接字符串。 然后，你可将这些值存储在单独的[参数文件](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values)中，以方便更改这些值。

* 在逻辑应用的资源定义内部、工作流定义外部，`parameters` 节指定了工作流定义参数的值。 在此节中，可以使用引用模板参数的模板表达式来分配这些值。 这些表达式将在部署时计算。

* 在工作流定义中，`parameters` 节定义了逻辑应用在运行时使用的参数。 然后，你可以使用在运行时计算的工作流定义表达式，在逻辑应用工作流中引用这些参数。

此示例模板包含使用 `securestring` 类型的多个受保护参数定义：

| 参数名称 | 描述 |
|----------------|-------------|
| `TemplatePasswordParam` | 一个模板参数，它接受随后要传递到工作流定义的 `basicAuthPasswordParam` 参数的密码 |
| `TemplateUsernameParam` | 一个模板参数，它接受随后要传递到工作流定义的 `basicAuthUserNameParam` 参数的用户名 |
| `basicAuthPasswordParam` | 一个工作流定义参数，它接受 HTTP 操作中用于基本身份验证的密码 |
| `basicAuthUserNameParam` | 一个工作流定义参数，它接受 HTTP 操作中用于基本身份验证的用户名 |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
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
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
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
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
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
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>对从逻辑应用调用的服务和系统的访问

下面是当逻辑应用需要访问发送请求时可用于保护终结点的一些方法：

* 针对出站请求添加身份验证。

  使用 HTTP、HTTP + Swagger（开放 API）或 Webhook 操作时，可以为逻辑应用发送的请求添加身份验证。 例如，可以使用基本身份验证、证书身份验证或 Azure Active Directory 身份验证。 有关详细信息，请参阅[对触发器或操作进行身份验证](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

* 限制从逻辑应用 IP 地址进行访问。

  从逻辑应用对终结点发出的所有调用都来源于基于逻辑应用区域的指定 IP 地址。 可以添加仅接受来自这些 IP 地址的请求的筛选规则。 若要获取这些 IP 地址，请参阅 [Azure 逻辑应用的限制和配置](logic-apps-limits-and-config.md#configuration)。

* 以托管标识的身份对资源进行身份验证和访问。

  若要在不登录的情况下访问其他 Azure Active Directory (Azure AD) 租户中的资源并对你的标识进行身份验证，逻辑应用可以使用托管标识（以前称为托管服务标识或 MSI），而非使用凭据或机密。 由于无需提供或轮换机密，因此 Azure 会为你管理此标识，并且会帮助保护凭据。 有关如何为逻辑应用设置并使用系统分配的托管标识的详细信息, 请参阅[在 Azure 逻辑应用中使用托管标识对资源进行身份验证和访问](../logic-apps/create-managed-service-identity.md)。

* 安全连接到本地系统。

  Azure 逻辑应用可与这些服务集成，提供安全可靠的本地通信。

  * 本地数据网关

    Azure 逻辑应用中的许多托管连接器都可以安全连接到本地系统，例如，文件系统、SQL、SharePoint 和 DB2。 网关通过 Azure 服务总线发送来自加密通道上的本地源的数据。 所有流量最初都是网关代理的安全出站流量。 了解[本地数据网关的工作原理](logic-apps-gateway-install.md#gateway-cloud-service)。

  * 通过 Azure API 管理进行连接

    [Azure API 管理](../api-management/api-management-key-concepts.md)提供本地连接选项，例如站点到站点虚拟专用网络和 ExpressRoute 集成，用于保护代理和与本地系统的通信。 在逻辑应用设计器的逻辑应用工作流中，可以选择 API 管理公开的 API，以快速访问本地系统。

## <a name="next-steps"></a>后续步骤

* [创建部署模板](logic-apps-create-deploy-template.md)  
* [监视逻辑应用](logic-apps-monitor-your-logic-apps.md)  
* [诊断逻辑应用的错误和问题](logic-apps-diagnosing-failures.md)  
* [自动完成逻辑应用部署](logic-apps-azure-resource-manager-templates-overview.md)
