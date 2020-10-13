---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: e3732823be1c8391f2bec9018a094200c7c93a5e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711157"
---
| properties | 说明 |
|:--- |:---|
|**identity / type** | 用来发出请求的身份验证的类型。 例如：`OAuth`、`SAS Key`、`Account Key` 或 `Anonymous` |
|**identity / tokenHash**|此字段为保留字段，仅供内部使用。 |
|**authorization / action** | 分配给请求的操作。 |
|**authorization / roleAssignmentId** | 角色分配 ID。 例如：`4e2521b7-13be-4363-aeda-111111111111`。|
|**authorization / roleDefinitionId** | 角色定义 ID。 例如：`ba92f5b4-2d11-453d-a403-111111111111"`。|
|**principals / id** | 安全主体的 ID。 例如：`a4711f3a-254f-4cfb-8a2d-111111111111`。|
|**principals / type** | 安全主体的类型。 例如：`ServicePrincipal`。 |
|**requester / appID** | 用作请求者的 Open Authorization (OAuth) 应用程序 ID。 <br> 例如：`d3f7d5fe-e64a-4e4e-871d-333333333333`。|
|**requester / audience** | 请求的 OAuth 受众。 例如：`https://storage.azure.com`。 |
|**requester / objectId** | 请求者的 OAuth 对象 ID。 对于 Kerberos 身份验证，此项表示已经过 Kerberos 身份验证的用户的对象标识符。 例如：`0e0bf547-55e5-465c-91b7-2873712b249c`。 |
|**requester / tenantId** | 标识的 OAuth 租户 ID。 例如：`72f988bf-86f1-41af-91ab-222222222222`。|
|**requester / tokenIssuer** | OAuth 令牌颁发者。 例如：`https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`。|
|**requester / upn** | 请求者的用户主体名称 (UPN)。 例如：`someone@contoso.com`。 |
|**requester / userName** | 此字段为保留字段，仅供内部使用。|