---
title: 跨租户进行身份验证
description: 介绍了 Azure 资源管理器如何跨租户处理身份验证请求。
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 7a13ba6f6cbfc10c52484c45e4011da7a0d8ee4c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75478820"
---
# <a name="authenticate-requests-across-tenants"></a>跨租户对请求进行身份验证

创建多租户应用程序时，可能需要处理针对不同租户中的资源的身份验证请求。 一个租户中的虚拟机必须加入另一个租户中的虚拟网络是一种常见情况。 Azure 资源管理器提供了一个标头值来存储用于对针对不同租户的请求进行身份验证的辅助令牌。

## <a name="header-values-for-authentication"></a>用于身份验证的标头值

请求具有以下身份验证标头值：

| 标头名称 | Description | 示例值 |
| ----------- | ----------- | ------------ |
| 授权 | 主令牌 | Bearer &lt;primary-token&gt; |
| x-ms-authorization-auxiliary | 辅助令牌 | 持有者 &lt;辅助 token1&gt;，EncryptedBearer &lt;token2&gt;，持有者 &lt;token3&gt; |

辅助标头最多可以容纳三个辅助令牌。 

在多租户应用的代码中，获取其他租户的身份验证令牌并将其存储在辅助标头中。 所有令牌都必须来自同一用户或应用程序。 该用户或应用程序必须已被作为来宾邀请到其他租户。

## <a name="processing-the-request"></a>处理请求

当应用向资源管理器发送请求时，该请求将以主令牌中的身份运行。 主令牌必须有效且未过期。 此令牌必须来自可以管理订阅的租户。

当请求引用了其他租户的资源时，资源管理器会检查辅助令牌来确定是否可以处理该请求。 标头中的所有辅助标记必须有效且未过期。 如果有任何令牌已过期，则资源管理器将返回 401 响应代码。 响应包括无效令牌中的客户端 ID 和租户 ID。 如果辅助标头包含租户的有效令牌，则会处理跨租户请求。

## <a name="next-steps"></a>后续步骤

* 若要了解身份验证请求，请参阅[身份验证流和应用程序方案](../../active-directory/develop/authentication-flows-app-scenarios.md)。
* 有关令牌的详细信息，请参阅 [Azure Active Directory 访问令牌](../../active-directory/develop/access-tokens.md)。
