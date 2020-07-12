---
title: 保护访问和数据
description: 保护对输入、输出、基于请求的触发器、运行历史记录、管理任务的访问，以及对 Azure 逻辑应用中的其他资源的访问
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 07/03/2020
ms.openlocfilehash: 902c48f2edcca6eb25958a9f22d6760faf1fcbc2
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86248707"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>保护 Azure 逻辑应用中的访问和数据

Azure 逻辑应用依赖[Azure 存储](https://docs.microsoft.com/azure/storage/)来存储和自动[加密静态数据](../security/fundamentals/encryption-atrest.md)。 此加密可保护你的数据，并可帮助你满足组织的安全性和符合性承诺。 默认情况下，Azure 存储使用 Microsoft 托管的密钥来加密数据。 有关详细信息，请参阅[静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)。

若要在 Azure 逻辑应用中进一步控制访问和保护敏感数据，你可以在以下方面设置其他安全性：

* [对基于请求的触发器的访问](#secure-triggers)
* [对逻辑应用操作的访问](#secure-operations)
* [对运行历史记录输入和输出的访问](#secure-run-history)
* [对参数输入的访问](#secure-action-parameters)
* [对从逻辑应用调用的服务和系统的访问](#secure-outbound-requests)
* [阻止为特定连接器创建连接](#block-connections)
* [逻辑应用的隔离指南](#isolation-logic-apps)
* [Azure 逻辑应用的 azure 安全基线](../logic-apps/security-baseline.md)

有关 Azure 中的安全性的详细信息，请参阅以下主题：

* [Azure 加密概述](../security/fundamentals/encryption-overview.md)
* [Azure 静态数据加密](../security/fundamentals/encryption-atrest.md)
* [Azure 安全基准](../security/benchmarks/overview.md)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>对基于请求的触发器的访问

如果逻辑应用使用基于请求的接收传入调用或请求的触发器（如[请求](../connectors/connectors-native-reqres.md)或 [Webhook](../connectors/connectors-native-webhook.md) 触发器）时，你可以限制访问权限，以便只有经过授权的客户端才能调用逻辑应用。 逻辑应用收到的所有请求都使用传输层安全性 (TLS) 协议进行加密和保护，之前称为安全套接字层 (SSL) 。

以下选项可帮助你保护对此触发器类型的访问：

* [生成共享访问签名](#sas)
* [启用 Azure Active Directory 开放式身份验证 (Azure AD OAuth)](#enable-oauth)
* [限制入站 IP 地址](#restrict-inbound-ip-addresses)
* [添加 Azure Active Directory 开放式身份验证 (Azure AD OAuth) 或其他安全性](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>生成共享访问签名 (SAS)

逻辑应用的每个请求终结点在终结点的 URL 中都包含一个[共享访问签名 (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas)，该 URL 的格式如下：

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

每个 URL 包含下表中所述的 `sp`、`sv` 和 `sig` 查询参数：

| 查询参数 | 说明 |
|-----------------|-------------|
| `sp` | 指定允许的 HTTP 方法使用的权限。 |
| `sv` | 指定用于生成签名的 SAS 版本。 |
| `sig` | 指定用于对触发器访问进行身份验证的签名。 此签名是使用 SHA256 算法生成的，所有 URL 路径和属性中都包含机密访问密钥。 该密钥永远不会公开或发布，而是一直处于加密状态并存储在逻辑应用中。 逻辑应用仅向那些包含有效签名（使用密钥创建）的触发器授权。 |
|||

有关使用 SAS 保护访问的详细信息，请参阅本主题中的以下部分：

* [重新生成访问密钥](#access-keys)
* [创建具有到期日期的回调 URL](#expiring-urls)
* [使用主密钥或辅助密钥创建 URL](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>重新生成访问密钥

若要随时生成新的安全访问密钥，请使用 Azure REST API 或 Azure 门户。 以前使用旧密钥生成的所有 URL 已失效，不再有权触发逻辑应用。 重新生成后检索的 URL 将使用新访问密钥进行签名。

1. 在 [Azure 门户](https://portal.azure.com)中，打开包含要重新生成密钥的逻辑应用。

1. 在逻辑应用菜单的“设置”下，选择“访问密钥”**** ****。

1. 选择要重新生成的密钥并完成生成过程。

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>创建具有到期日期的回调 URL

如果与其他各方共享基于请求的触发器的终结点 URL，可以生成使用特定密钥并具有过期日期的回调 URL。 这样，便可以无缝地滚动密钥，或基于特定的时间范围限制对触发逻辑应用的访问。 若要为 URL 指定过期日期，请使用[逻辑应用 REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers)，例如：

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在正文中，使用 JSON 日期字符串包含 `NotAfter` 属性。 该属性返回仅在 `NotAfter` 日期和时间之前有效的回调 URL。

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>创建附带主密钥或辅助密钥的 URL

生成或列出基于请求的触发器的回调 URL 时，可以指定用于对 URL 进行签名的密钥。 若要生成由特定密钥签名的 URL，请使用[逻辑应用 REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers)，例如：

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在正文中，包含值为 `Primary` 或 `Secondary` 的 `KeyType` 属性。 此属性返回由指定的安全密钥签名的 URL。

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-oauth"></a>启用 Azure Active Directory OAuth

如果逻辑应用以[请求触发器](../connectors/connectors-native-reqres.md)开始，则可以通过创建对请求触发器的入站调用的授权策略，启用[Azure Active Directory 开放身份验证](/azure/active-directory/develop/) (Azure AD OAuth) 。 在启用此身份验证之前，请查看以下注意事项：

* 对逻辑应用的入站调用只能使用一个授权方案，Azure AD OAuth 或[共享访问签名 (SAS)](#sas)。 只有请求触发器支持的[持有者类型](../active-directory/develop/active-directory-v2-protocols.md#tokens)授权方案仅受支持。

* 逻辑应用限制为最大授权策略数。 每个授权策略还具有最大[声明](../active-directory/develop/developer-glossary.md#claim)数。 有关详细信息，请参阅 [Azure 逻辑应用的限制和配置](../logic-apps/logic-apps-limits-and-config.md#authentication-limits)。

* 授权策略必须至少包含**颁发者**声明，该声明的值以 `https://sts.windows.net/` 或 `https://login.microsoftonline.com/` (OAuth V2) 作为 Azure AD 颁发者 ID。 有关访问令牌的详细信息，请参阅[Microsoft 标识平台访问令牌](../active-directory/develop/access-tokens.md)。

若要启用 Azure AD OAuth，请按照以下步骤将一个或多个授权策略添加到逻辑应用。

1. 在 [Azure 门户](https://portal.microsoft.com)的逻辑应用设计器中查找并打开逻辑应用。

1. 在逻辑应用菜单的“设置”下，选择“授权”**** ****。 打开“授权”窗格后，选择“添加策略”****。

   ![选择“授权”>“添加策略”](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. 在对请求触发器的每次入站调用提供的身份验证令牌中，逻辑应用需要一些[声明类型](../active-directory/develop/developer-glossary.md#claim)和值，通过指定这些声明类型和值来提供有关授权策略的信息：

   ![提供授权策略的信息](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | 策略名称**** | 适合 | 要用于授权策略的名称 |
   | **申请** | 是 | 逻辑应用从入站调用接受的声明类型和值。 下面是可用的声明类型： <p><p>- 颁发者**** <br>- 受众**** <br>- **主题** <br>- JWT ID（JSON Web 令牌 ID）**** <p><p>**声明**列表必须至少包含**颁发者**声明，该声明的值以或开头， `https://sts.windows.net/` `https://login.microsoftonline.com/` 作为 Azure AD 颁发者 ID。 有关这些声明类型的详细信息，请参阅 [Azure AD 安全令牌中的声明](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens)。 你还可以指定自己的声明类型和值。 |
   |||

1. 若要添加其他声明，请从以下选项中进行选择：

   * 若要添加其他声明类型，请选择“添加标准声明”，选择声明类型，并指定声明值****。

   * 若要添加自己的声明，请选择“添加自定义声明”，并指定自定义声明值****。

1. 若要添加其他授权策略，请选择“添加策略”****。 重复前面的步骤以设置策略。

1. 完成后，选择“保存”。

逻辑应用现已设置为使用 Azure AD OAuth 来授权入站请求。 逻辑应用收到包含身份验证令牌的入站请求时，Azure 逻辑应用会对照每个授权策略中的声明来比较令牌的声明。 如果令牌的声明与至少一个策略中的所有声明之间存在匹配项，则入站请求的授权成功。 令牌的声明数可以大于授权策略指定的声明数。

例如，假定逻辑应用有一个需要两个声明类型（颁发者和受众）的授权策略。 此示例解码的[访问令牌](../active-directory/develop/access-tokens.md)包括以下声明类型：

```json
{
   "aud": "https://management.core.windows.net/",
   "iss": "https://sts.windows.net/<Azure-AD-issuer-ID>/",
   "iat": 1582056988,
   "nbf": 1582056988,
   "exp": 1582060888,
   "_claim_names": {
      "groups": "src1"
   },
   "_claim_sources": {
      "src1": {
         "endpoint": "https://graph.windows.net/7200000-86f1-41af-91ab-2d7cd011db47/users/00000-f433-403e-b3aa-7d8406464625d7/getMemberObjects"
    }
   },
   "acr": "1",
   "aio": "AVQAq/8OAAAA7k1O1C2fRfeG604U9e6EzYcy52wb65Cx2OkaHIqDOkuyyr0IBa/YuaImaydaf/twVaeW/etbzzlKFNI4Q=",
   "amr": [
      "rsa",
      "mfa"
   ],
   "appid": "c44b4083-3bb0-00001-b47d-97400853cbdf3c",
   "appidacr": "2",
   "deviceid": "bfk817a1-3d981-4dddf82-8ade-2bddd2f5f8172ab",
   "family_name": "Sophia Owen",
   "given_name": "Sophia Owen (Fabrikam)",
   "ipaddr": "167.220.2.46",
   "name": "sophiaowen",
   "oid": "3d5053d9-f433-00000e-b3aa-7d84041625d7",
   "onprem_sid": "S-1-5-21-2497521184-1604012920-1887927527-21913475",
   "puid": "1003000000098FE48CE",
   "scp": "user_impersonation",
   "sub": "KGlhIodTx3XCVIWjJarRfJbsLX9JcdYYWDPkufGVij7_7k",
   "tid": "72f988bf-86f1-41af-91ab-2d7cd011db47",
   "unique_name": "SophiaOwen@fabrikam.com",
   "upn": "SophiaOwen@fabrikam.com",
   "uti": "TPJ7nNNMMZkOSx6_uVczUAA",
   "ver": "1.0"
}
```

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>限制入站 IP 地址

除了共享访问签名 (SAS) 以外，还可以限制可调用逻辑应用的特定客户端。 例如，如果使用[AZURE API 管理](../api-management/api-management-key-concepts.md)来管理请求终结点，则可以将逻辑应用限制为仅接受[所创建的 API 管理服务实例](../api-management/get-started-create-service-instance.md)的 IP 地址发出的请求。

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>在 Azure 门户中限制入站 IP 范围

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用的菜单中，在“设置”**** 下，选择“工作流设置”****。

1. 在“访问控制配置” > “允许的入站 IP 地址”下，选择“特定 IP 范围”**** **** ****。

1. 在“触发器的 IP 范围”下，请指定触发器接受的 IP 地址范围****。

   有效的 IP 范围使用这些格式：x.x.x.x/x 或 x.x.x.x-x.x.x.x** **

如果想让逻辑应用仅作为嵌套逻辑应用触发，请从“允许的入站 IP 地址”列表中选择“仅限其他逻辑应用”**** ****。 此选项会将空数组写入逻辑应用资源。 这样，只有来自逻辑应用服务（父逻辑应用）的调用才能触发嵌套逻辑应用。

> [!NOTE]
> 无论 IP 地址如何，你仍可以使用[逻辑应用 REST API：工作流触发器-运行](https://docs.microsoft.com/rest/api/logic/workflowtriggers/run)请求或使用 API 管理，来运行具有基于请求的触发器的逻辑应用。 不过，这种情况下仍需要针对 Azure REST API 进行[身份验证](../active-directory/develop/authentication-scenarios.md)。 所有事件都会显示在 Azure 审核日志中。 请确保相应地设置访问控制策略。

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>在 Azure 资源管理器模板中限制入站 IP 范围

如果[使用资源管理器模板自动执行逻辑应用部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，则可以通过使用 `accessControl` 部分并在逻辑应用的资源定义中包含 `triggers` 和 `actions` 部分来指定采用 x.x.x.x/x 或 x.x.x.x-x.x.x.x 格式的 IP 范围，例如** **：

```json
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
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               },
               "actions": {
                  "allowedCallerIpAddresses:" : []
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-open-authentication-or-other-security"></a>添加 Azure Active Directory 开放式身份验证或其他安全性

若要向逻辑应用添加更多[身份验证](../active-directory/develop/authentication-scenarios.md)协议，请考虑使用 [Azure API 管理](../api-management/api-management-key-concepts.md)服务。 此服务可帮助你将逻辑应用公开为 API，并为所有终结点提供丰富的监视信息、安全性、策略和文档。 API 管理可以公开逻辑应用的公共或专用终结点。 若要授予对此终结点的访问权限，可以使用 [Azure Active Directory 开放式身份验证](#azure-active-directory-oauth-authentication) (Azure AD OAuth)、[客户端证书](#client-certificate-authentication)或其他安全标准来授权对该终结点的访问权限。 当 API 管理收到请求时，此服务会将请求发送到逻辑应用，同时也会进行任何必要的转换或限制。 要仅让 API 管理触发逻辑应用，可以使用逻辑应用的入站 IP 范围设置。

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>对逻辑应用操作的访问

可以仅允许特定的用户或组运行特定的任务，例如管理、编辑和查看逻辑应用。 若要控制其权限，请使用 [Azure 基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-portal.md)，以便能够为 Azure 订阅中的成员分配自定义角色或内置角色：

* [逻辑应用参与者](../role-based-access-control/built-in-roles.md#logic-app-contributor)：允许管理逻辑应用，但不允许更改其访问权限。

* [逻辑应用操作员](../role-based-access-control/built-in-roles.md#logic-app-operator)：允许你读取、启用和禁用逻辑应用，但不能对其进行编辑或更新。

要防止他人更改或删除逻辑应用，可以使用 [Azure 资源锁](../azure-resource-manager/management/lock-resources.md)。 此功能可以防止他人更改或删除生产资源。

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>对运行历史记录数据的访问

在逻辑应用运行期间，所有数据都是使用传输层安全性 (TLS) [在传输过程中加密的](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)以及[静态](../security/fundamentals/encryption-atrest.md)加密的。 运行逻辑应用后，可以查看该运行的历史记录，包括已运行的步骤以及每个操作的状态、持续时间、输入和输出。 此丰富的详细信息可让你深入了解逻辑应用的运行方式，以及开始解决出现的任何问题的位置。

查看逻辑应用的运行历史记录时，逻辑应用将对该访问进行身份验证，然后提供指向请求的输入和输出以及每个运行的响应的链接。 但是，对于处理任何密码、机密、密钥或其他敏感信息的操作，你希望防止他人查看和访问该数据。 例如，如果逻辑应用从 [Azure Key Vault](../key-vault/general/overview.md) 获取机密，以便在对 HTTP 操作进行身份验证时使用，则你需要在视图中隐藏该机密。

若要控制对逻辑应用运行历史记录中的输入和输出的访问，可使用以下选项：

* [按 IP 地址范围限制访问](#restrict-ip)。

  此选项可帮助你基于来自特定 IP 地址范围的请求保护对运行历史记录的访问。

* [使用模糊处理保护运行历史记录中的数据](#obfuscate)。

  在许多触发器和操作中，可以保护逻辑应用的运行历史记录中的输入和/或输出。

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>按 IP 地址范围限制访问

可以限制对逻辑应用运行历史记录中的输入和输出的访问，以便只有来自特定 IP 地址范围的请求才能查看这些数据。 例如，若要阻止任何人访问输入和输出，请指定类似于 `0.0.0.0-0.0.0.0` 的 IP 地址范围。 只有拥有管理员权限的人员才能去除此限制，使“实时”访问逻辑应用的数据成为可能。 可以使用 Azure 门户或者在用于逻辑应用部署的 Azure 资源管理器模板中指定要限制的 IP 范围。

#### <a name="restrict-ip-ranges-in-azure-portal"></a>在 Azure 门户中限制 IP 范围

1. 在 Azure 门户的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用的菜单中，在“设置”**** 下，选择“工作流设置”****。

1. 在“访问控制配置” > “允许的入站 IP 地址”下，选择“特定 IP 范围”**** **** ****。

1. 在“内容的 IP 范围”下，指定可以访问输入和输出中内容的 IP 地址范围****。

   有效的 IP 范围使用这些格式：x.x.x.x/x 或 x.x.x.x-x.x.x.x** **

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>在 Azure 资源管理器模板中限制 IP 范围

如果[使用资源管理器模板自动执行逻辑应用部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，则可以通过在逻辑应用的资源定义中结合使用 `accessControl` 和 `contents` 部分来指定 IP 范围，例如：

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

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>使用模糊处理保护运行历史记录中的数据

许多触发器和操作具有用于保护逻辑应用的运行历史记录中的输入和/或输出的设置。 在使用这些设置帮助你保护此数据之前，请[查看以下注意事项](#obfuscation-considerations)。

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>保护设计器中的输入和输出

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

   ![在逻辑应用设计器中打开逻辑应用](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. 在要保护敏感数据的触发器或操作中，选择省略号 ( **...** ) 按钮，然后选择“设置”****。

   ![打开触发器或操作设置](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. 打开“安全输入”和/或“安全输出”**** ****。 完成后，选择“完成”****。

   ![打开“安全输入”或“安全输出”](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   操作或触发器现在显示标题栏中的锁图标。

   ![操作或触发器标题栏显示锁形图标](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   表示以前操作的安全输出的令牌也显示锁图标。 例如，从动态内容列表中选择要在操作中使用的此类输出时，该令牌将显示锁图标。

   ![为安全输出选择令牌](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. 运行逻辑应用后，可以查看该运行的历史记录。

   1. 在逻辑应用的”概述“窗格中，选择要查看的运行。****

   1. 在“逻辑应用运行”窗格中，展开要查看的操作****。

      如果选择同时遮盖输入和输出，这些值现在将处于隐藏状态。

      ![隐藏运行历史记录中的输入和输出](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>保护代码视图中的输入和输出

在基础触发器或操作定义中，使用以下两个值中的一个或两个值添加或更新 `runtimeConfiguration.secureData.properties` 数组：

* `"inputs"`：在运行历史记录中保护输入。
* `"outputs"`：保护运行历史记录中的输出。

使用这些设置帮助你保护此数据时，请[查看以下注意事项](#obfuscation-considerations)。

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

#### <a name="considerations-when-securing-inputs-and-outputs"></a>保护输入和输出时的注意事项

* 遮盖触发器或操作的输入或输出时，逻辑应用不会将安全数据发送到 Azure Log Analytics。 此外，不能将[跟踪的属性](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)添加到该触发器或操作进行监视。

* [用于处理工作流历史记录的逻辑应用 API](https://docs.microsoft.com/rest/api/logic/) 不会返回受保护的输出。

* 若要保护可遮盖输入或显式遮盖输出的操作的输出，请在该操作中手动打开“安全输出”****。

* 请确保在你希望运行历史记录来遮盖该数据的下游操作中打开“安全输入”或“安全输出”**** ****。

  安全输出设置****

  在触发器或操作中手动打开“安全输出”时，逻辑应用会隐藏运行历史记录中的这些输出****。 如果下游操作显式使用这些安全输出作为输入，则逻辑应用会隐藏运行历史记录中的此操作的输入，但不会启用操作的“安全输入”设置******。

  ![用作输入的受保护输出，以及对大多数操作的下游影响](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  “撰写”、“分析 JSON”和“响应”操作仅提供“保护输入”设置。**** 启用后，此设置也会隐藏这些操作的输出。 如果这些操作显式使用上游的受保护输出作为输入，则逻辑应用会隐藏这些操作的输入和输出，但不会启用这些操作的“保护输入”设置。****** 如果下游操作显式使用“撰写”、“分析 JSON”或“响应”操作中隐藏的输出作为输入，则逻辑应用不会隐藏此下游操作的输入或输出。**

  ![用作输入的受保护输出，以及对特定操作的下游影响](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  安全输入设置****

  在触发器或操作中手动打开“安全输入”时，逻辑应用会隐藏运行历史记录中的这些输入****。 如果下游操作显式使用该触发器或操作中的可见输出作为输入，则逻辑应用会隐藏运行历史记录中的此下游操作的输入，但不会启用此操作中的“安全输入”，并且不会隐藏此操作的输出******。

  ![受保护的输入以及对大多数操作的下游影响](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  如果“撰写”、“分析 JSON”和“响应”操作显式使用具有受保护输入的触发器或操作中的可见输出，则逻辑应用将隐藏这些操作的输入和输出，但不会启用这些操作的“保护输入”设置。****** 如果下游操作显式使用“撰写”、“分析 JSON”或“响应”操作中隐藏的输出作为输入，则逻辑应用不会隐藏此下游操作的输入或输出。**

  ![受保护的输入以及对特定操作的下游影响](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>对参数输入的访问

如果跨不同的环境进行部署，请考虑参数化工作流定义中因这些环境而有所不同的值。 这样，便可以通过使用 [Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)部署逻辑应用来避免使用硬编码数据，通过定义安全的参数来保护敏感数据，并使用[参数文件](../azure-resource-manager/templates/parameter-files.md)通过[模板的参数](../azure-resource-manager/templates/template-parameters.md)将该数据作为单独输入传递。

例如，如果使用 [Azure Active Directory 开放式身份验证](#azure-active-directory-oauth-authentication) (Azure AD OAuth) 对 HTTP 操作进行身份验证，则可以定义并遮盖接受用于身份验证的客户端 ID 和客户端机密的参数。 若要在逻辑应用中定义这些参数，请使用逻辑应用的工作流定义中的 `parameters` 部分，以及用于部署的资源管理器模板。 若要帮助保护在编辑逻辑应用或查看运行历史记录时不希望显示的参数值，请使用 `securestring` 或 `secureobject` 类型定义参数并根据需要使用编码。 具有此类型的参数不会随资源定义一起返回，并且在部署后无法通过查看资源来访问这些参数。 若要在运行时访问这些参数值，请在工作流定义中使用 `@parameters('<parameter-name>')` 表达式。 此表达式仅在运行时进行计算，由[工作流定义语言](../logic-apps/logic-apps-workflow-definition-language.md)描述。

> [!NOTE]
> 如果在请求标头或正文中使用参数，查看逻辑应用的运行历史记录和传出的 HTTP 请求时，该参数可能是可见的。 请务必同时相应地设置内容访问策略。
> 还可以使用[模糊处理](#obfuscate)在运行历史记录中隐藏输入和输出。 始终不能通过输入或输出看见授权标头。 因此，如果在此处使用机密，则无法检索该机密。

有关详细信息，请参阅本主题中的以下部分：

* [在工作流定义中保护参数](#secure-parameters-workflow)
* [使用模糊处理保护运行历史记录中的数据](#obfuscate)

如果[使用资源管理器模板自动执行逻辑应用部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)，则可以使用 `securestring` 和 `secureobject` 类型定义在部署时计算的安全[模板参数](../azure-resource-manager/templates/template-parameters.md)。 若要定义模板参数，请使用模板的顶级 `parameters` 节，该节不同于工作流定义的 `parameters` 节。 若要提供模板参数的值，请使用单独的[参数文件](../azure-resource-manager/templates/parameter-files.md)。

例如，如果使用机密，则可以定义并使用可在部署时从 [Azure Key Vault](../key-vault/general/overview.md) 检索这些机密的受保护模板参数。 然后，可以在参数文件中引用 Key Vault 和机密。 有关详细信息，请参阅以下主题：

* [在部署时使用 Azure Key Vault 传递敏感值](../azure-resource-manager/templates/key-vault-parameter.md)
* 本主题后面的[在 Azure 资源管理器模板中保护参数](#secure-parameters-deployment-template)

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

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>在 Azure 资源管理器模板中保护参数

逻辑应用的[资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)包含多个 `parameters` 节。 若要保护密码、密钥、机密和其他敏感信息，请使用 `securestring` 或 `secureobject` 类型在模板级别和工作流定义级别定义安全的参数。 然后，可将这些值存储在 [Azure Key Vault](../key-vault/general/overview.md) 中，并使用[参数文件](../azure-resource-manager/templates/parameter-files.md)来引用 Key Vault 和机密。 模板便会在部署时检索该信息。 有关详细信息，请参阅[在部署时使用 Azure Key Vault 传递敏感值](../azure-resource-manager/templates/key-vault-parameter.md)。

下面详细介绍了这些 `parameters` 部分：

* 在模板的最高级别，`parameters` 节定义了模板在部署时使用的值的参数。** 例如，这些值可能包含特定部署环境的连接字符串。 然后，你可将这些值存储在单独的[参数文件](../azure-resource-manager/templates/parameter-files.md)中，以方便更改这些值。

* 在逻辑应用的资源定义内部、工作流定义外部，`parameters` 节指定了工作流定义参数的值。 在此节中，可以使用引用模板参数的模板表达式来分配这些值。 这些表达式将在部署时计算。

* 在工作流定义中，`parameters` 节定义了逻辑应用在运行时使用的参数。 然后，你可以使用在运行时计算的工作流定义表达式，在逻辑应用工作流中引用这些参数。

此示例模板包含使用 `securestring` 类型的多个受保护参数定义：

| 参数名称 | 说明 |
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

<a name="secure-outbound-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>对从逻辑应用调用的服务和系统的访问

下面是可帮助保护接收来自逻辑应用的调用或请求的终结点的一些方法：

* 向出站请求添加身份验证。

  使用基于 HTTP 的触发器或发出出站调用的操作（如 HTTP、HTTP + Swagger 或 Webhook）时，可以向逻辑应用发送的请求添加身份验证。 例如，可以选择以下身份验证类型：

  * [基本身份验证](#basic-authentication)

  * [客户端证书身份验证](#client-certificate-authentication)

  * [Active Directory OAuth 身份验证](#azure-active-directory-oauth-authentication)

  * [托管标识身份验证](#managed-identity-authentication)

  有关详细信息，请参阅本主题稍后的[针对出站调用添加身份验证](#add-authentication-outbound)。

* 限制来自逻辑应用 IP 地址的访问。

  从逻辑应用对终结点发出的所有调用都来源于基于逻辑应用区域的指定 IP 地址。 可以添加仅接受来自这些 IP 地址的请求的筛选规则。 若要获取这些 IP 地址，请参阅 [Azure 逻辑应用的限制和配置](logic-apps-limits-and-config.md#configuration)。

* 提高与本地系统的连接的安全性。

  Azure 逻辑应用可与这些服务集成，来帮助提供更安全可靠的本地通信。

  * 本地数据网关

    Azure 逻辑应用中的许多托管连接器都可以安全连接到本地系统，例如，文件系统、SQL、SharePoint 和 DB2。 网关通过 Azure 服务总线发送来自加密通道上的本地源的数据。 所有流量最初都是网关代理的安全出站流量。 了解[本地数据网关的工作原理](logic-apps-gateway-install.md#gateway-cloud-service)。

  * 通过 Azure API 管理进行连接

    [AZURE API 管理](../api-management/api-management-key-concepts.md)提供本地连接选项，例如站点到站点虚拟专用网络和[ExpressRoute](../expressroute/expressroute-introduction.md)集成，以实现安全的代理和与本地系统的通信。 如果你有提供对本地系统的访问权限的 API，并且通过创建[Api 管理服务实例](../api-management/get-started-create-service-instance.md)公开了该 api，则可以通过在逻辑应用设计器中选择内置 API 管理触发器或操作来在逻辑应用的工作流中调用该 api。

    > [!NOTE]
    > 连接器仅显示您有权查看和连接的 API 管理服务，但不显示基于消耗的 API 管理服务。

    1. 在逻辑应用设计器中，在 `api management` 搜索框中输入。 根据你是要添加触发器还是操作来选择步骤：<p>

       * 如果要添加触发器，该触发器始终是工作流中的第一步，请选择 "**选择 AZURE API 管理触发器**"。

       * 如果要添加操作，请选择 "**选择 AZURE API 管理操作**"。

       此示例将添加一个触发器：

       ![添加 Azure API 管理触发器](./media/logic-apps-securing-a-logic-app/select-api-management.png)

    1. 选择以前创建的 API 管理服务实例。

       ![选择 API 管理服务实例](./media/logic-apps-securing-a-logic-app/select-api-management-service-instance.png)

    1. 选择要使用的 API 调用。

       ![选择现有 API](./media/logic-apps-securing-a-logic-app/select-api.png)

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>针对出站调用添加身份验证

HTTP 和 HTTPS 终结点支持各种身份验证。 在用于将出站调用或请求发送到这些终结点的某些触发器和操作上，可以指定身份验证类型。 在逻辑应用设计器中，支持选择身份验证类型的触发器和操作具有**身份验证**属性。 但是，默认情况下，此属性可能不会始终出现。 在这些情况下，在 "触发器" 或 "操作" 上，打开 "**添加新参数**" 列表，然后选择 "**身份验证**"。

> [!IMPORTANT]
> 若要保护逻辑应用处理的敏感信息，请使用受保护的参数，并根据需要对数据进行编码。
> 有关使用和保护参数的详细信息，请参阅[对参数输入的访问](#secure-action-parameters)。

此表标识了可以在其中选择身份验证类型的触发器和操作上可用的身份验证类型：

| 身份验证类型 | 可用性 |
|---------------------|--------------|
| [基本](#basic-authentication) | Azure API 管理、Azure 应用服务、HTTP、HTTP + Swagger、HTTP Webhook |
| [客户端证书](#client-certificate-authentication) | Azure API 管理、Azure 应用服务、HTTP、HTTP + Swagger、HTTP Webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API 管理、Azure 应用服务、Azure Functions、HTTP、HTTP + Swagger、HTTP Webhook |
| [原始](#raw-authentication) | Azure API 管理、Azure 应用服务、Azure Functions、HTTP、HTTP + Swagger、HTTP Webhook |
| [托管的标识](#managed-identity-authentication) | Azure API 管理、Azure 应用服务、Azure Functions、HTTP、HTTP + Swagger、HTTP Webhook |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>基本身份验证

如果[基本](../active-directory-b2c/secure-rest-api.md)选项可用，请指定以下属性值：

| 属性（设计器） | 属性 (JSON) | 必须 | 值 | 说明 |
|---------------------|-----------------|----------|-------|-------------|
| **身份验证** | `type` | 适合 | 基本 | 要使用的身份验证类型 |
| **用户名** | `username` | 是 | <*user-name*>| 用于对目标服务终结点访问进行身份验证的用户名 |
| **密码** | `password` | 适合 | <*password*> | 用于对目标服务终结点访问进行身份验证的密码 |
||||||

使用[安全参数](#secure-action-parameters)处理和保护敏感信息时，例如，在[用于自动执行部署的 Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中，可以使用表达式在运行时访问这些参数值。 此示例 HTTP 操作定义将身份验证 `type` 指定为 `Basic` 并使用 [parameters() function](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 获取参数值：

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

| 属性（设计器） | 属性 (JSON) | 必须 | 值 | 说明 |
|---------------------|-----------------|----------|-------|-------------|
| **身份验证** | `type` | 适合 | **客户端证书** <br>或 <br>`ClientCertificate` | 可使用的身份验证类型。 可以通过[AZURE API 管理](../api-management/api-management-howto-mutual-certificates.md)来管理证书。 <p></p>**注意**：对于入站和出站调用，自定义连接器不支持基于证书的身份验证。 |
| **Pfx** | `pfx` | 适合 | <*encoded-pfx-file-content*> | 个人信息交换 (PFX) 文件中的 base64 编码内容 <p><p>若要将 PFX 文件转换为 base64 编码格式，可以使用 PowerShell 并执行以下步骤： <p>1.将证书内容保存到某个变量中： <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2.使用 `ToBase64String()` 函数转换证书内容，并将该内容保存到某个文本文件中： <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **密码** | `password`| 否 | <password-for-pfx-file>** | 用于访问 PFX 文件的密码 |
|||||

使用[安全参数](#secure-action-parameters)处理和保护敏感信息时，例如，在[用于自动执行部署的 Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中，可以使用表达式在运行时访问这些参数值。 此示例 HTTP 操作定义将身份验证 `type` 指定为 `ClientCertificate` 并使用 [parameters() function](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 获取参数值：

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

* [使用 Azure API 管理中的客户端证书身份验证提高 API 的安全性](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [使用 Azure API 管理中的客户端证书身份验证提高后端服务的安全性](../api-management/api-management-howto-mutual-certificates.md)
* [使用客户端证书提高 RESTfuL 服务的安全性](../active-directory-b2c/secure-rest-api.md)
* [应用程序身份验证的证书凭据](../active-directory/develop/active-directory-certificate-credentials.md)
* [在 Azure 应用服务中通过代码使用 TLS/SSL 证书](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-open-authentication"></a>Azure Active Directory 开放式身份验证

在请求触发器上，可以使用 [Azure Active Directory 开放式身份验证](/azure/active-directory/develop/) (Azure AD OAuth)，在为逻辑应用[设置 Azure AD 授权策略](#enable-oauth)后对传入调用进行身份验证。 对于提供 Active Directory OAuth 身份验证类型供你选择的所有其他触发器和操作，请指定以下属性值****：

| 属性（设计器） | 属性 (JSON) | 必须 | 值 | 说明 |
|---------------------|-----------------|----------|-------|-------------|
| **身份验证** | `type` | 适合 | **Active Directory OAuth** <br>或 <br>`ActiveDirectoryOAuth` | 可使用的身份验证类型。 逻辑应用当前遵循 [OAuth 2.0 协议](../active-directory/develop/v2-overview.md)。 |
| 颁发机构**** | `authority` | 否 | <*URL-for-authority-token-issuer*> | 提供身份验证令牌的颁发机构的 URL。 此值默认为 `https://login.windows.net`。 |
| 租户**** | `tenant` | 适合 | <*tenant-ID*> | Azure AD 租户的租户 ID |
| **受众** | `audience` | 适合 | <*resource-to-authorize*> | 要用于授权的资源，例如 `https://management.core.windows.net/` |
| **客户端 ID** | `clientId` | 是 | <*client-ID*> | 请求授权的应用的客户端 ID |
| 凭据类型**** | `credentialType` | 适合 | 证书 <br>或 <br>机密 | 客户端用来请求授权的凭据类型。 此属性和值不会显示在逻辑应用的基础定义中，但确定了为选定凭据类型显示的属性。 |
| **机密** | `secret` | 是，但仅适用于“机密”凭据类型 | <*client-secret*> | 用于请求授权的客户端密码 |
| **Pfx** | `pfx` | 是，但仅适用于“证书”凭据类型 | <*encoded-pfx-file-content*> | 个人信息交换 (PFX) 文件中的 base64 编码内容 |
| **密码** | `password` | 是，但仅适用于“证书”凭据类型 | <*password-for-pfx-file*> | 用于访问 PFX 文件的密码 |
|||||

使用[安全参数](#secure-action-parameters)处理和保护敏感信息时，例如，在[用于自动执行部署的 Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中，可以使用表达式在运行时访问这些参数值。 此示例 HTTP 操作定义将身份验证 `type` 指定为 `ActiveDirectoryOAuth`，将凭据类型指定为 `Secret`，并使用 [parameters() function](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 获取参数值：

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

如果**原始**选项可用，在必须使用不遵循 [OAuth 2.0 协议](https://oauth.net/2/)的[身份验证方案](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml)时，可以使用此身份验证类型。 使用此类型可以手动创建要与传出请求一起发送的授权标头值，并指定触发器或操作中的标头值。

例如，下面是遵循 [OAuth 1.0 协议](https://tools.ietf.org/html/rfc5849)的 HTTPS 请求的示例标头：

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

在支持原始身份验证的触发器或操作中指定以下属性值：

| 属性（设计器） | 属性 (JSON) | 必须 | 值 | 说明 |
|---------------------|-----------------|----------|-------|-------------|
| **身份验证** | `type` | 适合 | 原始 | 要使用的身份验证类型 |
| **值** | `value` | 适合 | <*authorization-header-value*> | 要用于身份验证的授权标头值 |
||||||

使用[安全参数](#secure-action-parameters)处理和保护敏感信息时，例如，在[用于自动执行部署的 Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中，可以使用表达式在运行时访问这些参数值。 此示例 HTTP 操作定义将身份验证 `type` 指定为 `Raw` 并使用 [parameters() function](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 获取参数值：

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

如果 "[托管标识](../active-directory/managed-identities-azure-resources/overview.md)" 选项可用，则逻辑应用可以使用系统分配的标识，也可以使用*单个*手动创建的用户分配的标识来验证对受 Azure Active Directory (的其他资源的访问权限，而无需登录即可 Azure AD) 。 由于无需提供或轮换机密，因此 Azure 会为你管理此标识，并且会帮助保护凭据。 详细了解[支持 Azure AD 身份验证的托管标识的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

1. 在逻辑应用可以使用托管标识之前，请按照[使用 Azure 逻辑应用中的托管标识对 Azure 资源的访问进行身份验证](../logic-apps/create-managed-service-identity.md)中的步骤进行操作。 这些步骤将在逻辑应用上启用托管标识，并设置该标识对目标 Azure 资源的访问权限。

1. 在 Azure 函数可以使用托管标识之前，请先[为 Azure 函数启用身份验证](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)。

1. 在要使用托管标识的触发器或操作中，指定以下属性值：

   | 属性（设计器） | 属性 (JSON) | 必须 | 值 | 说明 |
   |---------------------|-----------------|----------|-------|-------------|
   | **身份验证** | `type` | 适合 | **托管标识** <br>或 <br>`ManagedServiceIdentity` | 要使用的身份验证类型 |
   | **托管标识** | `identity` | 是 | * 系统分配的托管标识**** <br>或 <br>`SystemAssigned` <p><p>* <user-assigned-identity-name>** | 要使用的托管标识 |
   | **受众** | `audience` | 适合 | <*target-resource-ID*> | 要访问的目标资源的资源 ID。 <p>例如，`https://storage.azure.com/` 使用于身份验证的[访问令牌](../active-directory/develop/access-tokens.md)对所有存储帐户都有效。 但是，还可以为特定存储帐户指定根服务 URL，如 `https://fabrikamstorageaccount.blob.core.windows.net`。 <p>**注意**：“受众”属性可能已在某些触发器或操作中隐藏****。 若要显示此属性，请在触发器或操作中打开“添加新参数”列表，然后选择“受众”**** ****。 <p><p>**重要说明**：请确保此目标资源 ID 完全匹配 Azure AD 所需的值，包括任何必需的尾部反斜杠**。 因此，所有 Azure Blob 存储帐户的 `https://storage.azure.com/` 资源 ID 都需要尾部反斜杠。 但是，特定存储帐户的资源 ID 不需要尾部斜杠。 若要查找这些资源 ID，请参阅[支持 Azure AD 的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。 |
   |||||

   使用[安全参数](#secure-action-parameters)处理和保护敏感信息时，例如，在[用于自动执行部署的 Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)中，可以使用表达式在运行时访问这些参数值。 此示例 HTTP 操作定义将身份验证 `type` 指定为 `ManagedServiceIdentity` 并使用 [parameters() function](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 获取参数值：

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

<a name="block-connections"></a>

## <a name="block-creating-connections"></a>阻止创建连接

如果你的组织不允许使用 Azure 逻辑应用中的连接器连接到特定资源，则可以使用[Azure 策略](../governance/policy/overview.md)阻止为逻辑应用工作流中的特定连接器[创建这些连接的功能](../logic-apps/block-connections-connectors.md)。 有关详细信息，请参阅[在 Azure 逻辑应用中阻止特定连接器创建的连接](../logic-apps/block-connections-connectors.md)。

<a name="isolation-logic-apps"></a>

## <a name="isolation-guidance-for-logic-apps"></a>逻辑应用的隔离指南

可以使用 azure[政府](../azure-government/documentation-government-welcome.md)版中的 Azure 逻辑应用，支持[Azure 政府影响级别5隔离指南](../azure-government/documentation-government-impact-level-5.md#azure-logic-apps)和[美国国防部云计算安全要求指南 (SRG) ](https://dl.dod.cyber.mil/wp-content/uploads/cloud/SRG/index.html)中描述的区域中的所有影响级别。 为了满足这些要求，逻辑应用支持在具有专用资源的环境中创建和运行工作流的功能，因此可以降低逻辑应用上其他 Azure 租户对性能的影响，并避免与其他租户共享计算资源。

* 若要运行自己的代码或执行 XML 转换，请[创建并调用 Azure 函数](../logic-apps/logic-apps-azure-functions.md)，而不是使用[内联代码功能](../logic-apps/logic-apps-add-run-inline-code.md)，或提供要分别用作[映射的程序集](../logic-apps/logic-apps-enterprise-integration-maps.md)。 此外，为 function app 设置托管环境，以符合隔离要求。

  例如，若要满足影响级别5的要求，请使用[**独立**定价层](../app-service/overview-hosting-plans.md)以及同时使用**独立**定价层的[应用服务环境 (ASE) ](../app-service/environment/intro.md) ，在[应用服务计划](../azure-functions/functions-scale.md#app-service-plan)中创建 function app。 在此环境中，函数应用在专用的 Azure 虚拟机和专用的 Azure 虚拟网络上运行，这些虚拟网络在计算隔离之上为你的应用提供网络隔离，并提供最大的横向扩展功能。 有关详细信息，请参阅[Azure 政府影响级别5隔离指南-Azure Functions](../azure-government/documentation-government-impact-level-5.md#azure-functions)。

  有关详细信息，请参阅以下主题：<p>

  * [Azure App Service 计划](../app-service/overview-hosting-plans.md)
  * [Azure Functions 网络选项](../azure-functions/functions-networking-options.md)
  * [适用于虚拟机的 Azure 专用主机](../virtual-machines/windows/dedicated-hosts.md)
  * [Azure 中的虚拟机隔离](../virtual-machines/windows/isolation.md)
  * [将专用 Azure 服务部署到虚拟网络](../virtual-network/virtual-network-for-azure-services.md)

* 若要创建在专用资源上运行的逻辑应用，并可访问受 Azure 虚拟网络保护的资源，可以[ (ISE) 创建 integration service 环境](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

  * 某些 Azure 虚拟网络使用专用终结点 ([Azure 专用链接](../private-link/private-link-overview.md)) 以便提供对 azure PaaS 服务（例如 azure 存储、Azure Cosmos DB 或 Azure SQL 数据库、合作伙伴服务或托管在 azure 上的客户服务）的访问权限。 如果逻辑应用需要访问使用专用终结点的虚拟网络，则必须在 ISE 中创建、部署和运行这些逻辑应用。

  * 为了更好地控制 Azure 存储使用的加密密钥，可以使用[Azure Key Vault](../key-vault/general/overview.md)设置、使用和管理你自己的密钥。 此功能也称为 "创建自己的密钥" (BYOK) ，密钥称为 "客户托管密钥"。 有关详细信息，请参阅[设置客户管理的密钥，以便在 Azure 逻辑应用中 (ISEs) 为 integration service 环境加密静态数据](../logic-apps/customer-managed-keys-integration-service-environment.md)。

有关详细信息，请参阅以下主题：

* [Azure 公有云中的隔离](../security/fundamentals/isolation-choices.md)
* [Azure 中高度敏感的 IaaS 应用的安全性](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/high-security-iaas)

## <a name="next-steps"></a>后续步骤

* [Azure 逻辑应用的 azure 安全基线](../logic-apps/security-baseline.md)
* [自动执行 Azure 逻辑应用部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [监视逻辑应用](../logic-apps/monitor-logic-apps-log-analytics.md)
