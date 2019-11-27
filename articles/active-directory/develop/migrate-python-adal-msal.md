---
title: 适用于 Python 的 ADAL 到 MSAL 迁移指南 |Microsoft
description: 了解如何将 Azure Active Directory Authentication Library （ADAL） Python 应用迁移到适用于 Python 的 Microsoft 身份验证库（MSAL）。
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: henrikm
editor: twhitney
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b31000c8b5e64cbd0edb2fc062f1c6e077a75f4
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481928"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>适用于 Python 的 ADAL 到 MSAL 迁移指南

本文重点介绍需要做出哪些更改，才能迁移使用 Azure Active Directory 身份验证库 (ADAL) 的应用，使之使用 Microsoft 身份验证库 (MSAL)。

## <a name="difference-highlights"></a>差异重点说明

ADAL 适用于 Azure Active Directory v1.0 终结点。 Microsoft 身份验证库（MSAL）适用于 Microsoft 标识平台（以前称为 Azure Active Directory v2.0 终结点）。 Microsoft 标识平台与 Azure Active Directory v1.0 的不同之处在于：

支持：
  - 工作和学校帐户（Azure AD 预配帐户）
  - 个人帐户（例如 Outlook.com 或 Hotmail.com）
  - 通过 Azure AD B2C 产品/服务使用自己的电子邮件或社交身份（如 LinkedIn、Facebook、Google）的客户

- 与以下协议的标准兼容：
  - OAuth v2.0
  - OpenID Connect (OIDC)

有关更多详细信息，请参阅[Microsoft 标识平台（v2.0）终结点的不同之处](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)。

### <a name="scopes-not-resources"></a>范围不是资源

ADAL Python 获取资源的令牌，但 MSAL Python 获取作用域的令牌。 MSAL Python 中的 API 图面不再具有资源参数。 需要提供范围作为字符串列表，这些字符串声明所请求的权限和资源。 若要查看作用域的一些示例，请参阅[Microsoft Graph 的作用域](https://docs.microsoft.com/graph/permissions-reference)。

### <a name="error-handling"></a>错误处理。

用于 Python 的 Azure Active Directory 身份验证库（ADAL）使用 `AdalError` 的异常，以指示存在问题。 用于 Python 的 MSAL 通常使用错误代码。 有关详细信息，请参阅[MSAL For Python 错误处理](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python)。

### <a name="api-changes"></a>API 更改

下表列出了适用于 Python 的 ADAL 中的 API，以及要在用于 Python 的 MSAL 中使用的 API：

| 用于 Python API 的 ADAL  | 用于 Python API 的 MSAL |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication 或 ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| 不适用  | [get_authorization_request_url （）](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code （）](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent （）](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | 不适用 |
| [acquire_user_code （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow （）](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code)和[cancel_request_to_get_token_with_device_code （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow （）](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password （）](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials)和[acquire_token_with_client_certificate （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client （）](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| 不适用 | [acquire_token_on_behalf_of （）](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache （）](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| 不适用 | 具有持久性的缓存，可从[MSAL 扩展](https://github.com/marstr/original-microsoft-authentication-extensions-for-python)获得 |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>迁移 MSAL Python 的现有刷新令牌

Microsoft 身份验证库（MSAL）对刷新令牌的概念进行了抽象。 默认情况下，MSAL Python 提供内存中令牌缓存，因此你无需存储、查找或更新刷新令牌。 用户还将看到较少的登录提示，因为通常无需用户干预即可更新刷新令牌。 有关令牌缓存的详细信息，请参阅[MSAL For Python 中的自定义令牌缓存序列化](msal-python-token-cache-serialization.md)。

以下代码将帮助你将其他 OAuth2 库（包括但不限于 ADAL Python）管理的刷新令牌迁移为由 MSAL for Python 管理。 迁移这些刷新令牌的一个原因是，在将应用迁移到 MSAL for Python 时，阻止现有用户重新登录。

迁移刷新令牌的方法是使用 MSAL for Python 使用上一个刷新令牌获取新的访问令牌。 返回新的刷新令牌时，Python 的 MSAL 会将其存储在缓存中。 下面是有关如何执行此操作的示例：

```python
from msal import PublicClientApplication

def get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    raise NotImplementedError("You will need to implement this by yourself")

app = PublicClientApplication(..., token_cache=...)

for old_rt, old_scope in get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    # Assuming the old scope could be a space-delimited string.
    # MSAL expects a list, like ["scope1", "scope2"].
    scopes = old_scope.split()
        # If your old refresh token came from ADAL for Python, which uses a resource rather than a scope,
        # you need to convert your v1 resource into v2 scopes
        # See https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison#scopes-not-resources
        # You may be able to append "/.default" to your v1 resource to form a scope
        # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    result = app.client.obtain_token_by_refresh_token(old_rt, scope=scopes)
    # When this call returns the new token(s), a new refresh token is issued by the Microsoft identity platform and MSAL for Python
    # stores it in the token cache.
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请阅读 [v1.0 与 v2.0 的比较](active-directory-v2-compare.md)。
