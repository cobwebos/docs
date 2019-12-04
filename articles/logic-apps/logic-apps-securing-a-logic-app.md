---
title: 保护访问和数据
description: 添加安全性以保护输入、输出、基于请求的触发器、运行历史记录、管理任务，以及访问 Azure 逻辑应用中的其他资源
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 0e9b382b27d0bd1e4fd3a553ca468dd562eca368
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792909"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Azure 逻辑应用中的安全访问和数据

若要在 Azure 逻辑应用中控制访问和保护数据，你可以在以下方面设置安全性：

* [访问基于请求的触发器](#secure-triggers)
* [逻辑应用操作的访问权限](#secure-operations)
* [运行历史记录输入和输出的访问权限](#secure-run-history)
* [访问参数输入](#secure-action-parameters)
* [访问逻辑应用中调用的服务和系统](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>访问基于请求的触发器

如果逻辑应用使用基于请求的触发器来接收传入的调用或请求，如[请求](../connectors/connectors-native-reqres.md)或[Webhook](../connectors/connectors-native-webhook.md)触发器，则可以限制访问，以便只有经过授权的客户端才能调用逻辑应用。 逻辑应用接收到的所有请求都使用安全套接字层 (SSL) 协议进行加密和保护。

可以通过以下方式保护对此触发器类型的访问：

* [生成共享访问签名](#sas)
* [限制入站 IP 地址](#restrict-inbound-ip-addresses)
* [添加 Azure Active Directory OAuth 或其他安全性](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>生成共享访问签名（SAS）

逻辑应用上的每个请求终结点在终结点的 URL 中都有一个[共享访问签名（SAS）](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) ，其格式如下：

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

每个 URL 都包含此表中所述的 `sp`、`sv`和 `sig` 查询参数：

| 查询参数 | 描述 |
|-----------------|-------------|
| `sp` | 指定允许的 HTTP 方法使用的权限。 |
| `sv` | 指定用于生成签名的 SAS 版本。 |
| `sig` | 指定用于对触发器的访问进行身份验证的签名。 此签名是通过对所有 URL 路径和属性使用 SHA256 算法和密钥访问密钥生成的。 此密钥永远不会公开或发布，并与逻辑应用一起存储。 逻辑应用仅向那些包含有效签名（使用密钥创建）的触发器授权。 |
|||

有关使用 SAS 保护访问的详细信息，请参阅本主题中的以下部分：

* [重新生成访问密钥](#access-keys)
* [创建具有到期日期的回调 URL](#expiring-urls)
* [使用主密钥或辅助密钥创建 URL](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>重新生成访问密钥

若要随时生成新的安全访问密钥，请使用 Azure REST API 或 Azure 门户。 所有以前生成的使用旧密钥的 Url 均失效，不再有权触发逻辑应用。 重新生成后检索的 Url 会使用新的访问密钥进行签名。

1. 在[Azure 门户](https://portal.azure.com)中，打开包含要重新生成的密钥的逻辑应用。

1. 在逻辑应用菜单的“设置”下，选择“访问密钥”。

1. 选择要重新生成的密钥并完成该过程。

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>创建过期回调 Url

如果与其他参与方共享基于请求的触发器的终结点 URL，则可以生成使用特定密钥并具有到期日期的回调 Url。 这样，你就可以无缝地滚动密钥，或限制访问以根据特定时间跨度触发逻辑应用。 若要为 URL 指定过期日期，请使用[REST API 逻辑应用](https://docs.microsoft.com/rest/api/logic/workflowtriggers)，例如：

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在正文中，使用 JSON 日期字符串包含 `NotAfter`属性。 该属性返回仅在 `NotAfter` 日期和时间之前有效的回调 URL。

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>创建附带主密钥或辅助密钥的 URL

为基于请求的触发器生成或列出回调 Url 时，可以指定用于对 URL 进行签名的密钥。 若要生成由特定密钥签名的 URL，请使用[REST API 逻辑应用](https://docs.microsoft.com/rest/api/logic/workflowtriggers)，例如：

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在正文中，包含值为 `Primary` 或 `Secondary` 的 `KeyType` 属性。 此属性返回由指定的安全密钥签名的 URL。

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>限制入站 IP 地址

与共享访问签名（SAS）一起，你可能想要专门限制可调用逻辑应用的客户端。 例如，如果使用 Azure API 管理来管理请求终结点，则可以将逻辑应用限制为仅接受来自 API 管理实例的 IP 地址的请求。

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>限制 Azure 门户中的入站 IP 范围

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用的菜单中，在“设置”下，选择“工作流设置”。

1. 在“访问控制配置” > “允许的入站 IP 地址”下，请选择“特定 IP 范围”。

1. 在“触发器的 IP 范围”下，请指定触发器接受的 IP 地址范围。

   有效的 IP 范围使用这些格式：x.x.x.x/x 或 x.x.x.x-x.x.x.x

如果希望逻辑应用仅作为嵌套逻辑应用触发，请在 "**允许的入站 IP 地址**" 列表中，选择 "**仅限其他逻辑应用**"。 此选项将空数组写入逻辑应用资源。 这样一来，只有来自逻辑应用服务（父逻辑应用）的调用才能触发嵌套的逻辑应用。

> [!NOTE]
> 无论 IP 地址如何，仍可通过 Azure REST API 或 API 管理使用 `/triggers/<trigger-name>/run` 来运行具有基于请求的触发器的逻辑应用。 但是，此方案仍需要针对 Azure REST API 进行身份验证。 所有事件都显示在 Azure 审核日志中。 请确保相应地设置访问控制策略。

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>限制 Azure 资源管理器模板中的入站 IP 范围

如果[使用资源管理器模板自动部署逻辑应用](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，则可以通过将 `accessControl` 部分与逻辑应用的资源定义中的 `triggers` 部分结合使用来指定 IP 范围，例如：

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

### <a name="add-azure-active-directory-oauth-or-other-security"></a>添加 Azure Active Directory OAuth 或其他安全性

若要将更多授权协议添加到逻辑应用，请考虑使用[AZURE API 管理](../api-management/api-management-key-concepts.md)服务。 此服务可帮助你将逻辑应用作为 API 公开，并为任何终结点提供丰富的监视、安全、策略和文档。 API 管理可以公开逻辑应用的公共或专用终结点。 若要授予对此终结点的访问权限，可以使用[Azure Active Directory OAuth](#azure-active-directory-oauth-authentication)、[客户端证书](#client-certificate-authentication)或其他安全标准来授权访问该终结点。 当 API 管理收到请求时，此服务会将请求发送到逻辑应用，同时也会进行任何必要的转换或限制。 若要仅允许 API 管理触发逻辑应用，可以使用逻辑应用的入站 IP 范围设置。

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>逻辑应用操作的访问权限

你可以仅允许特定用户或组运行特定任务，如管理、编辑和查看逻辑应用。 要控制其权限，请使用[Azure 基于角色的访问控制（RBAC）](../role-based-access-control/role-assignments-portal.md) ，以便可以将自定义或内置角色分配给 Azure 订阅中的成员：

* [逻辑应用参与者](../role-based-access-control/built-in-roles.md#logic-app-contributor)：允许管理逻辑应用，但不能更改它们的访问权限。

* [逻辑应用操作员](../role-based-access-control/built-in-roles.md#logic-app-operator)：允许读取、启用和禁用逻辑应用，但不能对其进行编辑或更新。

要防止他人更改或删除逻辑应用，可以使用 [Azure 资源锁](../azure-resource-manager/resource-group-lock-resources.md)。 此功能可以防止其他人更改或删除生产资源。

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>运行历史记录数据的访问权限

在逻辑应用运行期间，在传输[过程中](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)使用传输层安全性（TLS）和[静态](../security/fundamentals/encryption-atrest.md)加密所有数据。 逻辑应用完成运行后，可以查看该运行的历史记录，包括运行的步骤以及每个操作的状态、持续时间、输入和输出。 此丰富的详细信息可让你深入了解逻辑应用的运行情况，以及你可以在何处开始解决出现的任何问题。

查看逻辑应用的运行历史记录时，逻辑应用会对访问进行身份验证，并提供每次运行的请求和响应的输入和输出的链接。 但是，对于处理任何密码、机密、密钥或其他敏感信息的操作，你希望阻止其他人查看和访问该数据。 例如，如果逻辑应用从[Azure Key Vault](../key-vault/key-vault-overview.md)获取要在对 HTTP 操作进行身份验证时使用的机密，则需要将该机密从视图中隐藏。

若要在逻辑应用的运行历史记录中控制对输入和输出的访问，你可以使用以下选项：

* [按 IP 地址范围限制访问](#restrict-ip)。

  此选项可让你基于特定 IP 地址范围内的请求来保护对运行历史记录的访问。

* [使用混淆隐藏运行历史记录中的数据](#obfuscate)。

  在许多触发器和操作中，可以从逻辑应用的运行历史记录中隐藏其输入和/或输出。

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>按 IP 地址范围限制访问

可以在逻辑应用的运行历史记录中限制对输入和输出的访问，以便只有来自特定 IP 地址范围的请求才能查看这些数据。 例如，若要阻止任何人访问输入和输出，请指定 IP 地址范围，如 `0.0.0.0-0.0.0.0`。 只有具有管理员权限的人员才可以删除此限制，这将为逻辑应用的数据提供 "实时" 访问的可能性。 你可以使用 Azure 门户或用于逻辑应用部署的 Azure 资源管理器模板指定要限制的 IP 范围。

#### <a name="restrict-ip-ranges-in-azure-portal"></a>限制 Azure 门户中的 IP 范围

1. 在 Azure 门户的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用的菜单中，在“设置”下，选择“工作流设置”。

1. 在“访问控制配置” > “允许的入站 IP 地址”下，请选择“特定 IP 范围”。

1. 在“内容的 IP 范围”下，指定可以访问输入和输出中内容的 IP 地址范围。 

   有效的 IP 范围使用这些格式：x.x.x.x/x 或 x.x.x.x-x.x.x.x

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>限制 Azure 资源管理器模板中的 IP 范围

如果[使用资源管理器模板自动部署逻辑应用](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，则可以通过将 `accessControl` 部分与逻辑应用的资源定义中的 `contents` 部分结合使用来指定 IP 范围，例如：

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

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>使用混淆隐藏运行历史记录中的数据

许多触发器和操作都具有从逻辑应用的运行历史记录中隐藏输入和/或输出的设置。 当你使用这些设置来保护此类数据时，请[注意以下事项](#obfuscation-considerations)。

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>设计器中的安全输入和输出

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

   ![在逻辑应用设计器中打开逻辑应用](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. 在要保护数据的触发器或操作上，选择省略号（ **...** ）按钮，然后选择 "**设置**"。

   ![打开触发器或操作设置](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. 启用**安全输入**和/或**安全输出**。 完成后，选择“完成”。

   ![启用安全输入或输出](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   操作或触发器现在在标题栏中显示锁定图标。

   ![操作或触发器标题栏显示锁定图标](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   表示以前操作的安全输出的标记也显示锁图标。 例如，从 "动态内容" 列表中选择要在操作中使用的此类输出时，该令牌将显示一个锁状图标。

   ![为安全输出选择标记](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. 逻辑应用运行后，可以查看该运行的历史记录。

   1. 在逻辑应用的 "**概述**" 窗格上，选择要查看的运行。

   1. 在 "**逻辑应用运行**" 窗格上，展开要查看的操作。

      如果选择保护输入和输出，则这些值现在显示为隐藏。

      ![运行历史记录中隐藏的输入和输出](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>在代码视图中保护输入和输出

在基础触发器或操作定义中，添加或更新其中一个或两个值的 `runtimeConfiguration.secureData.properties` 数组：

* `"inputs"`：保护运行历史记录中的输入。
* `"outputs"`：保护运行历史记录中的输出。

当你使用这些设置来保护此类数据时，请[注意以下事项](#obfuscation-considerations)。

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

* 当你保护触发器或操作的输入或输出时，逻辑应用不会将受保护的数据发送到 Azure Log Analytics。 此外，不能将[跟踪的属性](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details)添加到该触发器或操作进行监视。

* [用于处理工作流历史记录的逻辑应用 API](https://docs.microsoft.com/rest/api/logic/)不返回安全输出。

* 若要通过保护输入的操作保护输出，或显式使用安全输出，请在该操作中手动打开**安全输出**。

* 请确保在期望运行历史记录保护数据的下游操作中启用**安全输入**或**安全输出**。

  **安全输出设置**

  在触发器或操作中手动打开**安全输出**时，逻辑应用将在运行历史记录中保护这些输出。 如果下游操作显式使用这些安全输出作为输入，则逻辑应用会在运行历史记录中隐藏此操作的输入，但*不会启用*该操作的**安全输入**设置。

  ![作为输入以及对大多数操作的下游的影响的安全输出](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  撰写、分析 JSON 和响应操作仅具有**安全输入**设置。 启用后，此设置还将隐藏这些操作的输出。 如果这些操作显式使用上游安全输出作为输入，则逻辑应用会隐藏这些操作的输入和输出，但*不会启用*这些操作的**安全输入**设置。 如果下游操作显式使用来自撰写的隐藏输出，分析 JSON 或响应操作作为输入，则逻辑应用*不会隐藏此下游操作的输入或输出*。

  ![作为输入的安全输出，具有对特定操作的下游影响](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **安全输入设置**

  在触发器或操作中手动启用**安全输入**时，逻辑应用将在运行历史记录中保护这些输入。 如果下游操作显式使用该触发器或操作的可见输出作为输入，则逻辑应用会在运行历史记录中隐藏此下游操作的输入，但*不会*在此操作中启用**安全输入**，也不会隐藏此操作的输出。

  ![受保护的输入和下游对大多数操作的影响](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  如果撰写、分析 JSON 和响应操作显式使用具有受保护输入的触发器或操作中的可见输出，则逻辑应用会隐藏这些操作的输入和输出，但*不会启用*这些操作的**安全输入**设置。 如果下游操作显式使用来自撰写的隐藏输出，分析 JSON 或响应操作作为输入，则逻辑应用*不会隐藏此下游操作的输入或输出*。

  ![受保护的输入和对特定操作的下游影响](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>访问参数输入

如果在不同的环境中进行部署，请考虑在工作流定义中参数化根据这些环境而变化的值。 这样一来，你就可以通过使用[Azure 资源管理器模板](../azure-resource-manager/template-deployment-overview.md)来部署逻辑应用、通过定义受保护的参数来保护敏感数据，[并通过使用](../azure-resource-manager/template-parameters.md)[参数文件](../azure-resource-manager/resource-manager-parameter-files.md)将该数据作为单独的输入传递，来避免使用硬编码数据。

例如，如果使用[Azure Active Directory OAuth](#azure-active-directory-oauth-authentication)对 HTTP 操作进行身份验证，则可以定义接受用于身份验证的客户端 ID 和客户端密钥的参数并确保它们的安全。 若要在逻辑应用中定义这些参数，请使用逻辑应用的工作流定义中的 `parameters` 部分，并为部署资源管理器模板。 若要隐藏编辑逻辑应用或查看运行历史记录时不希望显示的参数值，请使用 `securestring` 或 `secureobject` 类型定义参数，并在必要时使用编码。 具有此类型的参数不会随资源定义一起返回，且在部署后查看资源时不可访问。 若要在运行时访问这些参数值，请使用工作流定义中的 `@parameters('<parameter-name>')` 表达式。 此表达式仅在运行时进行计算，由[工作流定义语言](../logic-apps/logic-apps-workflow-definition-language.md)进行描述。

> [!NOTE]
> 如果在请求标头或正文中使用参数，则在查看逻辑应用的运行历史记录和传出 HTTP 请求时，该参数可能可见。 请确保同时设置内容访问策略。 还可以使用[模糊](#obfuscate)处理在运行历史记录中隐藏输入和输出。 始终不能通过输入或输出看见授权标头。 因此，如果在此处使用机密，则无法检索该机密。

有关详细信息，请参阅本主题中的以下部分：

* [工作流定义中的安全参数](#secure-parameters-workflow)
* [使用混淆隐藏运行历史记录中的数据](#obfuscate)

如果[使用资源管理器模板自动部署逻辑应用](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，则可以通过使用 `securestring` 和 `secureobject` 类型定义在部署时计算的受保护[模板参数](../azure-resource-manager/template-parameters.md)。 若要定义模板参数，请使用模板的顶层 `parameters` 部分，该部分是独立的，不同于工作流定义的 `parameters` 部分。 若要提供模板参数的值，请使用单独的[参数文件](../azure-resource-manager/resource-manager-parameter-files.md)。

例如，如果使用机密，你可以定义和使用安全的模板参数，以便在部署时从[Azure Key Vault](../key-vault/key-vault-overview.md)检索这些机密。 然后，你可以在参数文件中引用密钥保管库和机密。 有关详细信息，请参阅以下主题：

* [使用 Azure Key Vault 在部署时传递敏感值](../azure-resource-manager/resource-manager-keyvault-parameter.md)
* 本主题后面的[Azure 资源管理器模板中的安全参数](#secure-parameters-deployment-template)

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>工作流定义中的安全参数

若要在逻辑应用的工作流定义中保护敏感信息，请使用受保护的参数，以便在保存逻辑应用后，此信息不可见。 例如，假设有一个 HTTP 操作需要使用用户名和密码的基本身份验证。 在工作流定义中，`parameters` 部分通过使用 `securestring` 类型定义 `basicAuthPasswordParam` 和 `basicAuthUsernameParam` 参数。 然后，操作定义将引用 `authentication` 部分中的这些参数。

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

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Azure 资源管理器模板中的安全参数

逻辑应用的[资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)包含多个 `parameters` 部分。 若要保护密码、密钥、机密和其他敏感信息，请使用 `securestring` 或 `secureobject` 类型在模板级别和工作流定义级别定义安全参数。 然后，你可以将这些值存储在[Azure Key Vault](../key-vault/key-vault-overview.md)中，并使用[参数文件](../azure-resource-manager/resource-manager-parameter-files.md)来引用密钥保管库和机密。 然后，模板会在部署时检索该信息。 有关详细信息，请参阅[在部署时使用 Azure Key Vault 传递敏感值](../azure-resource-manager/resource-manager-keyvault-parameter.md)。

下面是有关这些 `parameters` 部分的详细信息：

* 在模板的顶层，`parameters` 部分定义模板在*部署*时使用的值的参数。 例如，这些值可能包含特定部署环境的连接字符串。 然后，可以将这些值存储在单独的[参数文件](../azure-resource-manager/resource-manager-parameter-files.md)中，这会使更改这些值变得更容易。

* 在逻辑应用的资源定义中，但在工作流定义外，`parameters` 节指定工作流定义的参数的值。 在本部分中，可以使用引用模板参数的模板表达式来分配这些值。 这些表达式是在部署时计算的。

* 在工作流定义中，`parameters` 节定义逻辑应用在运行时使用的参数。 然后，可以使用在运行时计算的工作流定义表达式来引用逻辑应用工作流中的这些参数。

此示例模板具有使用 `securestring` 类型的多个安全参数定义：

| 参数名称 | 描述 |
|----------------|-------------|
| `TemplatePasswordParam` | 一个模板参数，它接受随后传递到工作流定义的 `basicAuthPasswordParam` 参数的密码 |
| `TemplateUsernameParam` | 一个模板参数，该参数接受用户名，然后将其传递给工作流定义的 `basicAuthUserNameParam` 参数 |
| `basicAuthPasswordParam` | 一个工作流定义参数，该参数接受 HTTP 操作中基本身份验证的密码 |
| `basicAuthUserNameParam` | 一个工作流定义参数，该参数接受 HTTP 操作中用于基本身份验证的用户名 |
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

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>访问逻辑应用中调用的服务和系统

可以通过以下方式保护接收来自逻辑应用的调用或请求的终结点：

* 将身份验证添加到出站请求。

  使用基于 HTTP 的触发器或执行出站调用的操作（如 HTTP、HTTP + Swagger 或 Webhook）时，可以向逻辑应用发送的请求添加身份验证。 例如，可以使用以下身份验证类型：

  * [基本身份验证](#basic-authentication)

  * [客户端证书身份验证](#client-certificate-authentication)

  * [Active Directory OAuth 身份验证](#azure-active-directory-oauth-authentication)

  * [托管标识身份验证](#managed-identity-authentication)
  
  有关详细信息，请参阅本主题后面的[将身份验证添加到出站调用](#add-authentication-outbound)。

* 限制对逻辑应用 IP 地址的访问。

  从逻辑应用对终结点进行的所有调用源自基于逻辑应用区域的特定指定 IP 地址。 可以添加仅接受来自这些 IP 地址的请求的筛选规则。 若要获取这些 IP 地址，请参阅[Azure 逻辑应用的限制和配置](logic-apps-limits-and-config.md#configuration)。

* 安全连接到本地系统。

  Azure 逻辑应用可与这些服务集成，提供安全可靠的本地通信。

  * 本地数据网关

    Azure 逻辑应用中的许多托管连接器提供与本地系统（如文件系统、SQL、SharePoint 和 DB2）的安全连接。 网关通过 Azure 服务总线发送来自加密通道上的本地源的数据。 所有流量最初都是网关代理的安全出站流量。 了解[本地数据网关的工作原理](logic-apps-gateway-install.md#gateway-cloud-service)。

  * 通过 Azure API 管理进行连接

    [Azure API 管理](../api-management/api-management-key-concepts.md)提供本地连接选项，例如站点到站点虚拟专用网络和 ExpressRoute 集成，用于保护代理和与本地系统的通信。 通过逻辑应用设计器中逻辑应用的工作流，你可以选择 API 管理公开的 API，它提供对本地系统的快速访问。

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>向出站呼叫添加身份验证

HTTP 和 HTTPS 终结点支持各种身份验证。 根据你用来发出出站调用或访问这些终结点的请求的触发器或操作，你可以从不同的身份验证类型范围中进行选择。 若要确保保护逻辑应用处理的任何敏感信息，请使用受保护的参数，并根据需要对数据进行编码。 有关使用和保护参数的详细信息，请参阅[访问参数输入](#secure-action-parameters)。

| 身份验证类型 | 支持者 |
|---------------------|--------------|
| [基本](#basic-authentication) | Azure API 管理，Azure 应用服务，HTTP，HTTP + Swagger，HTTP Webhook |
| [客户端证书](#client-certificate-authentication) | Azure API 管理，Azure 应用服务，HTTP，HTTP + Swagger，HTTP Webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API 管理，Azure 应用服务，Azure Functions，http，HTTP + Swagger，HTTP Webhook |
| [原材料](#raw-authentication) | Azure API 管理，Azure 应用服务，Azure Functions，http，HTTP + Swagger，HTTP Webhook |
| [托管标识](#managed-identity-authentication)（仅系统分配） | Azure API 管理，Azure 应用服务，Azure Functions，http，HTTP + Swagger，HTTP Webhook |
|||

> [!NOTE]
> 在逻辑应用设计器中，可以在某些触发器和操作中隐藏**身份验证**属性，你可以在其中指定身份验证类型。 若要使属性在这些情况下出现，请在 "触发器" 或 "操作" 中打开 "**添加新参数**" 列表，然后选择 "**身份验证**"。 有关详细信息，请参阅[使用托管身份验证访问权限](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)。

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>基本身份验证

如果 "[基本](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-basic.md)" 选项可用，请指定以下属性值：

| 属性（设计器） | 属性（JSON） | 需要 | Value | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| **身份验证** | `type` | 是 | 基本 | 要使用的身份验证类型 |
| **用户名** | `username` | 是 | <*用户名*>| 用于对目标服务终结点访问进行身份验证的用户名 |
| **密码** | `password` | 是 | <*密码*> | 用于对目标服务终结点访问进行身份验证的密码 |
||||||

使用[受保护的参数](#secure-action-parameters)处理和保护敏感信息（例如，在[用于自动部署的 Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中）时，可以在运行时使用表达式来访问这些参数值。 此示例 HTTP 操作定义将身份验证 `type` 指定为 `Basic` 并使用[parameters （）函数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)获取参数值：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>客户端证书身份验证

如果[客户端证书](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)选项可用，请指定以下属性值：

| 属性（设计器） | 属性（JSON） | 需要 | Value | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| **身份验证** | `type` | 是 | **客户端证书** <br>或 <br>`ClientCertificate` | 安全套接字层 (SSL) 客户端证书使用的身份验证类型。 虽然支持自签名证书，但不支持用于 SSL 的自签名证书。 |
| **Pfx** | `pfx` | 是 | <*编码-pfx-文件内容*> | 个人信息交换 (PFX) 文件中的 base64 编码内容 <p><p>若要将 PFX 文件转换为 base64 编码格式，可以通过执行以下步骤来使用 PowerShell： <p>1. 将证书内容保存到变量： <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. 通过使用 `ToBase64String()` 函数来转换证书内容，并将该内容保存到文本文件中： <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **密码** | `password`| 请参阅说明 | <*pfx 密码-文件*> | 用于访问 PFX 文件的密码。 <p><p>**注意**：在逻辑应用设计器中工作时，此属性值是必需的，在 "代码视图" 中工作时*不*需要此属性值。 |
|||||

使用[受保护的参数](#secure-action-parameters)处理和保护敏感信息（例如，在[用于自动部署的 Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中）时，可以在运行时使用表达式来访问这些参数值。 此示例 HTTP 操作定义将身份验证 `type` 指定为 `ClientCertificate` 并使用[parameters （）函数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)获取参数值：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

有关使用客户端证书身份验证保护服务的详细信息，请参阅以下主题：

* [在 Azure API 管理中使用客户端证书身份验证保护 Api](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [在 Azure API 管理中使用客户端证书身份验证保护后端服务](../api-management/api-management-howto-mutual-certificates.md)
* [使用客户端证书保护 RESTfuL 服务](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
* [用于应用程序身份验证的证书凭据](../active-directory/develop/active-directory-certificate-credentials.md)
* [在 Azure 应用服务的应用程序代码中使用 SSL 证书](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Azure Active Directory OAuth 身份验证

如果[Active Directory OAuth](../active-directory/develop/about-microsoft-identity-platform.md)选项可用，请指定以下属性值：

| 属性（设计器） | 属性（JSON） | 需要 | Value | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| **身份验证** | `type` | 是 | **Active Directory OAuth** <br>或 <br>`ActiveDirectoryOAuth` | 要使用的身份验证类型。 逻辑应用当前遵循[OAuth 2.0 协议](../active-directory/develop/v2-overview.md)。 |
| **组织** | `tenant` | 是 | <*tenant-ID*> | Azure AD 租户的租户 ID |
| **受众** | `audience` | 是 | <*resource-to-authorize*> | 要用于授权的资源，例如 `https://management.core.windows.net/` |
| **客户端 ID** | `clientId` | 是 | <*client-ID*> | 请求授权的应用的客户端 ID |
| **凭据类型** | `credentialType` | 是 | 证书 <br>或 <br>密钥 | 客户端用于请求授权的凭据类型。 此属性和值不会出现在逻辑应用的基础定义中，而是确定为所选凭据类型显示的属性。 |
| **机密** | `secret` | 是，但仅适用于 "机密" 凭据类型 | <*客户端-机密*> | 用于请求授权的客户端密码 |
| **Pfx** | `pfx` | 是，但仅用于 "证书" 凭据类型 | <*编码-pfx-文件内容*> | 个人信息交换 (PFX) 文件中的 base64 编码内容 |
| **密码** | `password` | 是，但仅用于 "证书" 凭据类型 | <*pfx 密码-文件*> | 用于访问 PFX 文件的密码 |
| **无权** | `authority` | No | <*URL-for-authority-token-issuer*> | 提供身份验证令牌的颁发机构的 URL。 此值默认为 `https://login.windows.net`。 <p>**注意**：若要使此属性在设计器中可见，请在 "触发器" 或 "操作" 中打开 "**添加新参数**" 列表，然后选择 "**授权**"。 |
|||||

使用[受保护的参数](#secure-action-parameters)处理和保护敏感信息（例如，在[用于自动部署的 Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中）时，可以在运行时使用表达式来访问这些参数值。 此示例 HTTP 操作定义将身份验证 `type` 指定为 `ActiveDirectoryOAuth`，将凭据类型指定为 `Secret`，并使用[parameters （）函数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)获取参数值：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

### <a name="raw-authentication"></a>原始身份验证

如果**原始**选项可用，则在必须使用不遵循[OAuth 2.0 协议](https://oauth.net/2/)的[身份验证方案](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml)时，可以使用此身份验证类型。 利用此类型，您可以手动创建与传出请求一起发送的授权标头值，并在您的触发器或操作中指定该标头值。

例如，下面是遵循[OAuth 1.0 协议](https://tools.ietf.org/html/rfc5849)的 HTTPS 请求的示例标头：

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

在支持原始身份验证的触发器或操作中，指定以下属性值：

| 属性（设计器） | 属性（JSON） | 需要 | Value | 描述 |
|---------------------|-----------------|----------|-------|-------------|
| **身份验证** | `type` | 是 | 原材料 | 要使用的身份验证类型 |
| **值** | `value` | 是 | <*authorization-标头值*> | 要用于身份验证的授权标头值 |
||||||

使用[受保护的参数](#secure-action-parameters)处理和保护敏感信息（例如，在[用于自动部署的 Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中）时，可以在运行时使用表达式来访问这些参数值。 此示例 HTTP 操作定义将身份验证 `type` 指定为 `Raw`，并使用[parameters （）函数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)获取参数值：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

### <a name="managed-identity-authentication"></a>托管标识身份验证

如果 "[托管标识](../active-directory/managed-identities-azure-resources/overview.md)" 选项可用，则逻辑应用可以使用系统分配的标识对其他 Azure Active Directory （Azure AD）租户中的资源进行身份验证，而无需登录。 由于无需提供或轮换机密，因此 Azure 会为你管理此标识，并且会帮助保护凭据。 详细了解[支持 Azure AD 身份验证的托管标识的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

1. 在逻辑应用可以使用系统分配的标识之前，请遵循在[Azure 逻辑应用中使用托管标识对 azure 资源的访问权限进行身份验证](../logic-apps/create-managed-service-identity.md)中的步骤。 这些步骤将在逻辑应用中启用托管标识，并设置该标识对目标 Azure 资源的访问权限。

2. 在 Azure 函数可以使用系统分配的标识之前，请先[为 azure 函数启用身份验证](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)。

3. 在要使用托管标识的触发器或操作中，指定以下属性值：

   | 属性（设计器） | 属性（JSON） | 需要 | Value | 描述 |
   |---------------------|-----------------|----------|-------|-------------|
   | **身份验证** | `type` | 是 | **托管的标识** <br>或 <br>`ManagedServiceIdentity` | 要使用的身份验证类型 |
   | **受众** | `audience` | 是 | <*目标资源 ID*> | 要访问的目标资源的资源 ID。 <p>例如，`https://storage.azure.com/` 使得用于身份验证的访问令牌对所有存储帐户都有效。 但是，你还可以为特定的存储帐户指定根服务 URL，如 `https://fabrikamstorageaccount.blob.core.windows.net`。 <p>**注意**：此属性可能会在某些触发器或操作中隐藏。 若要使此属性可见，请在 "触发器" 或 "操作" 中打开 "**添加新参数**" 列表，然后选择 "**受众**"。 <p><p>**重要提示**：请确保此目标资源 ID 与 Azure AD 预期的值完全匹配，其中包括所有必需的尾随斜杠。 因此，所有 Azure Blob 存储帐户的 `https://storage.azure.com/` 资源 ID 都需要尾部斜杠。 不过，特定存储帐户的资源 ID 不需要尾部斜杠。 若要查找这些资源 Id，请参阅[支持 Azure AD 的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。 |
   |||||

   使用[受保护的参数](#secure-action-parameters)处理和保护敏感信息（例如，在[用于自动部署的 Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中）时，可以在运行时使用表达式来访问这些参数值。 此示例 HTTP 操作定义将身份验证 `type` 指定为 `ManagedServiceIdentity` 并使用[parameters （）函数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)获取参数值：

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

## <a name="next-steps"></a>后续步骤

* [自动部署 Azure 逻辑应用](logic-apps-azure-resource-manager-templates-overview.md)  
* [监视逻辑应用](logic-apps-monitor-your-logic-apps.md)  
* [诊断逻辑应用的错误和问题](logic-apps-diagnosing-failures.md)  
* [自动部署逻辑应用](logic-apps-azure-resource-manager-templates-overview.md)
