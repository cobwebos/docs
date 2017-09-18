---
title: "保护对 Azure 逻辑应用的访问 | Microsoft 文档"
description: "添加安全机制来保护对 Azure 逻辑应用中工作流使用的触发器、输入和输出、操作参数以及服务的访问。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/22/2016
ms.author: LADocs; jehollan
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: 45a4e476f930e0f5f6633dc5b3b35b66dc6dfa20
ms.contentlocale: zh-cn
ms.lasthandoff: 09/07/2017

---

# <a name="secure-access-to-your-logic-apps"></a>安全访问逻辑应用

可以借助许多工具来保护逻辑应用。

* 保护触发逻辑应用（HTTP 请求触发器）的访问
* 保护管理、编辑或读取逻辑应用的访问
* 保护对运行应用时的输入和输出内容的访问
* 保护工作流中的操作的参数或输入
* 保护对从工作流接收请求的服务的访问

## <a name="secure-access-to-trigger"></a>保护对触发器的访问

在使用针对 HTTP 请求（[请求](../connectors/connectors-native-reqres.md)或 [Webhook](../connectors/connectors-native-webhook.md)）触发的逻辑应用时，可以限制访问，以便只有经过授权的客户端可以触发该逻辑应用。 发往逻辑应用的所有请求经过加密，并通过 SSL 受到保护。

### <a name="shared-access-signature"></a>共享访问签名

逻辑应用的每个请求终结点的 URL 包含[共享访问签名 (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。 每个 URL 包含 `sp`、`sv` 和 `sig` 查询参数。 权限由 `sp` 指定，对应于允许的 HTTP 方法；`sv` 是用于生成的版本，`sig` 用于对触发器访问进行身份验证。 签名是使用 SHA256 算法生成的，所有 URL 路径和属性中包含一个密钥。 该机密密钥会永远不会公开或向外发布，而是保留加密状态，存储为逻辑应用的一部分。 逻辑应用只会向包含有效签名（使用密钥创建）的触发器授权。

#### <a name="regenerate-access-keys"></a>重新生成访问密钥

随时可以通过 REST API 或 Azure 门户重新生成新的安全密钥。 以前使用旧密钥生成的所有当前 URL 已失效，不再有权触发逻辑应用。

1. 在 Azure 门户中，打开要重新生成密钥的逻辑应用
1. 单击“设置”下面的“访问密钥”菜单项
1. 选择要重新生成的密钥并完成生成过程

重新生成后检索的 URL 将使用新访问密钥进行签名。

#### <a name="creating-callback-urls-with-an-expiration-date"></a>创建附带过期日期的回调 URL

如果要与其他人共享 URL，可以根据需要生成具有特定密钥和过期日期的 URL。 这样便可以顺利地滚动更新密钥，或者将触发应用的访问活动限定为特定的时间范围。 可通过[逻辑应用 REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) 为 URL 指定过期日期：

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

在正文中，包含 JSON 日期字符串形式的属性 `NotAfter`，该属性返回只在 `NotAfter` 日期和时间之前有效的回调 URL。

#### <a name="creating-urls-with-primary-or-secondary-secret-key"></a>创建附带主要或辅助机密密钥的 URL

生成或列出基于请求的触发器的回调 URL 时，还可以指定要使用哪个密钥来为 URL 签名。  可按如下所示，通过[逻辑应用 REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) 生成由特定密钥签名的 URL：

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

在正文中，包含值为 `Primary` 或 `Secondary` 的属性 `KeyType`。  此属性将返回由指定的安全密钥签名的 URL。

### <a name="restrict-incoming-ip-addresses"></a>限制传入 IP 地址

除了共享访问签名以外，还可以限制为只能通过特定的客户端调用逻辑应用。  例如，如果要通过 Azure API 管理来管理终结点，可将逻辑应用限制为仅当请求来自 API 管理实例 IP 地址时，才接受该请求。

可在逻辑应用设置中配置此项设置：

1. 在 Azure 门户中，打开要添加 IP 地址限制的逻辑应用
1. 单击“设置”下面的“访问控制配置”菜单项
1. 指定触发器接受的 IP 地址范围列表

有效的 IP 范围采用 `192.168.1.1/255` 格式。 如果希望逻辑应用只作为嵌套逻辑应用触发，请选择“仅限其他逻辑应用”选项。 此选项将一个空数组写入资源，意味着只有来自服务本身（父逻辑应用）的调用才能成功触发。

> [!NOTE]
> 不管 IP 是什么，仍可通过 REST API/管理 `/triggers/{triggerName}/run` 运行包含请求触发器的逻辑应用。 在此情况下需要针对 Azure REST API 执行身份验证，所有事件会显示在 Azure 审核日志中。 请相应地设置访问控制策略。

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>在资源定义中设置 IP 范围

如果使用[部署模板](logic-apps-create-deploy-template.md)自动完成部署，可在资源模板中配置 IP 范围设置。  

``` json
{
    "properties": {
        "definition": {
        },
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
    "type": "Microsoft.Logic/workflows"
}

```

### <a name="adding-azure-active-directory-oauth-or-other-security"></a>添加 Azure Active Directory、OAuth 或其他安全标准

为了在逻辑应用顶层添加更多授权协议，[Azure API 管理](https://azure.microsoft.com/services/api-management/)为能够将逻辑应用作为 API 公开的任何终结点提供了丰富的监视、安全、策略和文档功能。 Azure API 管理可以公开逻辑应用的公共或专用终结点，该终结点可以利用 Azure Active Directory、证书、OAuth 或其他安全标准。 收到请求时，Azure API 管理将请求转发到逻辑应用（即时执行任何所需的转换或限制）。 可以使用逻辑应用中的传入 IP 范围设置，限制为只能通过 API 管理触发逻辑应用。

## <a name="secure-access-to-manage-or-edit-logic-apps"></a>保护对逻辑应用管理或编辑功能的访问

可以限制对逻辑应用中管理操作的访问，以便只有特定的用户或组才能对资源执行操作。 逻辑应用使用 Azure [基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-configure.md) 功能，用户可以使用相同的工具对它进行自定义。  还可以将订阅的成员分配到几个内置角色：

* **逻辑应用参与者** - 提供查看、编辑和更新逻辑应用的访问权限。  无法删除资源或执行管理操作。
* **逻辑应用操作员** - 可以查看逻辑应用和运行历史记录，以及启用/禁用相关功能。  无法编辑或更新定义。

还可以使用 [Azure 资源锁](../azure-resource-manager/resource-group-lock-resources.md)来防止更改或删除逻辑应用。 此功能非常有用，它可以防止修改或删除生产资源。

## <a name="secure-access-to-contents-of-the-run-history"></a>保护对运行历史记录内容的访问

可以限制为只能通过特定的 IP 地址范围访问以前运行应用时提供的输入或输出内容。  

工作流运行中的所有数据都已经过传输中加密和静态加密。 发出运行历史记录的调用时，服务将对请求进行身份验证，并提供请求和响应输入与输出的链接。 可以保护此链接，以便只有来自指定 IP 地址范围的内容查看请求才能返回内容。 此功能可用于其他访问控制。 甚至可以指定类似于 `0.0.0.0` 的 IP 地址，使得任何人都无法访问输入/输出。 只有拥有管理员权限的人员才能解除此限制，使“适时”访问工作流内容成为可能。

可以在 Azure 门户的资源设置中配置此项设置：

1. 在 Azure 门户中，打开要添加 IP 地址限制的逻辑应用
1. 单击“设置”下面的“访问控制配置”菜单项
1. 指定有权访问内容的 IP 地址范围列表

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>在资源定义中设置 IP 范围

如果使用[部署模板](logic-apps-create-deploy-template.md)自动完成部署，可在资源模板中配置 IP 范围设置。  

``` json
{
    "properties": {
        "definition": {
        },
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
    "type": "Microsoft.Logic/workflows"
}
```

## <a name="secure-parameters-and-inputs-within-a-workflow"></a>保护工作流中的参数和输入

对于跨环境部署，可在某些方面将工作流定义参数化。 此外，其中的一些参数可能是安全参数，不希望在编辑工作流时显示这些参数，例如，用于对 HTTP 操作进行 [Azure Active Directory 身份验证](../connectors/connectors-native-http.md#authentication)的客户端 ID 和客户端密码。

### <a name="using-parameters-and-secure-parameters"></a>使用参数和安全参数

[工作流定义语言](http://aka.ms/logicappsdocs)提供一项 `@parameters()` 操作用于在运行时访问资源参数的值。 此外，可[在资源部署模板中指定参数](../azure-resource-manager/resource-group-authoring-templates.md#parameters)。 如果将参数类型指定为 `securestring`，它不会连同资源定义的剩余部分一起返回，并且在部署后无法通过查看资源来访问该参数。

> [!NOTE]
> 如果在标头或请求正文中使用参数，在访问运行历史记录和传出 HTTP 请求时，可能会看到该参数。 请务必相应地设置内容访问策略。
> 始终不能通过输入或输出看见授权标头。 因此，如果在输入或输出中使用密钥，则无法检索密钥。

#### <a name="resource-deployment-template-with-secrets"></a>包含机密的资源部署模板

以下示例显示在运行时引用安全参数 `secret` 的部署。 在单独的参数文件中，可以指定 `secret` 的环境值，或者在部署时利用 [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) 检索密钥。

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "secretDeploymentParam": {
      "type": "securestring"
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "secret-deploy",
      "type": "Microsoft.Logic/workflows",
      "location": "westus",
      "tags": {
        "displayName": "LogicApp"
      },
      "apiVersion": "2016-06-01",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "actions": {
            "Call_External_API": {
              "type": "http",
              "inputs": {
                "headers": {
                  "Authorization": "@parameters('secret')"
                },
                "body": "This is the request"
              },
              "runAfter": {}
            }
          },
          "parameters": {
            "secret": {
              "type": "SecureString"
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
          "secret": {
            "value": "[parameters('secretDeploymentParam')]"
          }
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="secure-access-to-services-receiving-requests-from-a-workflow"></a>保护对从工作流接收请求的服务的访问

可以借助多种方法来保护逻辑应用需要访问的任何终结点。

### <a name="using-authentication-on-outbound-requests"></a>针对出站请求使用身份验证

使用 HTTP、HTTP + Swagger（开放 API）或 Webhook 操作时，可将身份验证添加到正在发送的请求。 该信息可能包括基本身份验证、证书身份验证或 Azure Active Directory 身份验证。 [此文](../connectors/connectors-native-http.md#authentication)中提供了有关如何配置这种身份验证的详细信息。

### <a name="restricting-access-to-logic-app-ip-addresses"></a>限制对逻辑应用 IP 地址的访问

通过逻辑应用发出的所有调用都来自于每个区域中一组特定 IP 地址。 可以添加附加的筛选，以便只接受来自这些指定 IP 地址的请求。 有关这些 IP 地址的列表，请参阅[逻辑应用限制和配置](logic-apps-limits-and-config.md#configuration)。

### <a name="on-premises-connectivity"></a>本地连接

可将逻辑应用与一些服务集成，提供安全可靠的本地通信。

#### <a name="on-premises-data-gateway"></a>本地数据网关

使用逻辑应用的多个托管连接器可以安全连接到本地系统，包括文件系统、SQL、SharePoint、 DB2，等等。 网关通过 Azure 服务总线中继来自加密频道上的本地源的数据。 所有流量最初都是网关代理的安全出站流量。 详细了解[数据网关的工作原理](logic-apps-gateway-install.md#gateway-cloud-service)。

#### <a name="azure-api-management"></a>Azure API 管理

[Azure API 管理](https://azure.microsoft.com/services/api-management/)提供本地连接选项，包括站点到站点 VPN 和 ExpressRoute 集成，可以保护代理与本地系统的通信。 在逻辑应用设计器中，可以快速选择工作流中通过 Azure API 管理公开的 API，从而快速访问本地系统。

## <a name="next-steps"></a>后续步骤
[创建部署模板](logic-apps-create-deploy-template.md)  
[异常处理](logic-apps-exception-handling.md)  
[监视逻辑应用](logic-apps-monitor-your-logic-apps.md)  
[诊断逻辑应用的错误和问题](logic-apps-diagnosing-failures.md)  

