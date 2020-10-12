---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/21/2020
ms.author: glenga
ms.openlocfilehash: 380878fedaa2f7ea6160c3c4801c8dece6e1a9ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83648964"
---
#### <a name="enable-app-service-authenticationauthorization"></a>启用应用服务身份验证/授权

借助应用服务平台可以使用 Azure Active Directory (AAD) 和多个第三方标识提供者对客户端进行身份验证。 可以使用此策略来实现函数的自定义授权规则，并且可以从函数代码处理用户信息。 若要了解详细信息，请参阅 [Azure 应用服务中的身份验证和授权](../articles/app-service/overview-authentication-authorization.md)以及[使用客户端标识](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities)。

#### <a name="use-azure-api-management-apim-to-authenticate-requests"></a>使用 Azure API 管理 (APIM) 对请求进行身份验证

APIM 为传入请求提供了各种 API 安全选项。 若要了解详细信息，请参阅 [API 管理身份验证策略](../articles/api-management/api-management-authentication-policies.md)。 有了 APIM，可以配置函数应用以接受仅来自 APIM 实例 IP 地址的请求。 若要了解详细信息，请参阅 [IP 地址限制](../articles/azure-functions/ip-addresses.md#ip-address-restrictions)。
