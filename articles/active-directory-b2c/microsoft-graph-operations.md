---
title: 支持的 Microsoft Graph 操作
titleSuffix: Azure AD B2C
description: 支持的用于管理 Azure AD B2C 资源（包括用户、用户流、标识提供者、自定义策略、策略密钥，等等）的 Microsoft Graph 操作的索引。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d8d898d3825fa40cbfd13337067c24cf14f9a544
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102011"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>可用于 Azure AD B2C 的 Microsoft Graph 操作

支持用于管理 Azure AD B2C 资源（包括用户、标识提供者、用户流、自定义策略和策略密钥）的下列 Microsoft Graph API 操作。

以下各部分中的每个链接对应于 Microsoft Graph API 参考中该操作的相应页面。

## <a name="user-management"></a>用户管理

- [列出用户](https://docs.microsoft.com/graph/api/user-list)
- [创建使用者用户](https://docs.microsoft.com/graph/api/user-post-users)
- [获取用户](https://docs.microsoft.com/graph/api/user-get)
- [更新用户](https://docs.microsoft.com/graph/api/user-update)
- [删除用户](https://docs.microsoft.com/graph/api/user-delete)

有关使用 Microsoft Graph API 管理 Azure AD B2C 用户帐户的详细信息，请参阅[使用 Microsoft Graph 管理 Azure AD B2C 用户帐户](manage-user-accounts-graph-api.md)。

## <a name="user-phone-number-management"></a>用户电话号码管理

- [添加](https://docs.microsoft.com/graph/api/authentication-post-phonemethods)
- [Get](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-get)
- [更新](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-update)
- [删除](https://docs.microsoft.com/graph/api/phoneauthenticationmethod-delete)

若要详细了解如何通过 Microsoft Graph API 管理用户的登录电话号码，请参阅 [B2C 身份验证方法](https://docs.microsoft.com/graph/api/resources/b2cauthenticationmethodspolicy)。

## <a name="identity-providers-user-flow"></a> (用户流的标识提供程序) 

管理 Azure AD B2C 租户中用户流可用的标识提供者。

- [列出在 Azure AD B2C 租户中注册的标识提供者](https://docs.microsoft.com/graph/api/identityprovider-list)
- [创建标识提供者](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [获取标识提供者](https://docs.microsoft.com/graph/api/identityprovider-get)
- [更新标识提供者](https://docs.microsoft.com/graph/api/identityprovider-update)
- [删除标识提供者](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>用户流

为注册、登录、组合注册和登录、密码重置和配置文件更新配置预先生成的策略。

- [列出用户流](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [创建用户流](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [获取用户流](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [删除用户流](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>自定义策略

以下操作可用于管理 Azure AD B2C 信任框架策略（称为 [自定义策略](custom-policy-overview.md)）。

- [列出租户中配置的所有信任框架策略](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [创建信任框架策略](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [读取现有信任框架策略的属性](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [更新或创建信任框架策略。](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [删除现有的信任框架策略](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>策略密钥

标识体验框架存储在自定义策略中引用的机密，以建立组件之间的信任关系。 这些机密可以是对称或非对称密钥/值。 在 Azure 门户中，这些实体显示为 **策略密钥**。

Microsoft Graph API 中策略密钥的顶级资源是 [受信任的框架键集](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset)。 每个 **键集** 至少包含一个 **键**。 若要创建密钥，请先创建一个空的键集，然后在键集中生成一个密钥。 你可以创建手动机密、上传证书或 PKCS12 密钥。 密钥可以是生成的机密、你定义的字符串 (如 Facebook 应用程序机密) 或上传的证书。 如果键集具有多个键，则仅有一个键处于活动状态。

### <a name="trust-framework-policy-keyset"></a>信任框架策略键集

- [列出信任框架键集](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [创建信任框架键集](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [获取键集](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [更新信任框架键集](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [删除信任框架键集](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>信任框架策略密钥

- [在键集中获取当前活动的密钥](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [在键集中生成密钥](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [上传基于字符串的机密](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [上传 x.509 证书](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [上传 PKCS12 格式证书](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>应用程序

- [列出应用程序](https://docs.microsoft.com/graph/api/application-list)
- [创建应用程序](https://docs.microsoft.com/graph/api/resources/application)
- [更新应用程序](https://docs.microsoft.com/graph/api/application-update)
- [创建 servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [创建 oauth2Permission Grant](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [删除应用程序](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>应用程序扩展属性

- [列出扩展属性](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C 提供一个目录，其中可以保存每个用户的 100 个自定义属性。 对于用户流，将 [使用 Azure 门户管理](custom-policy-custom-attributes.md)这些扩展属性。 对于自定义策略，Azure AD B2C 会为你创建属性，这是首次向扩展属性写入值。

## <a name="audit-logs"></a>审核日志

- [列出审核日志](https://docs.microsoft.com/graph/api/directoryaudit-list)

有关通过 Microsoft Graph API 访问 Azure AD B2C 审核日志的详细信息，请参阅 [访问 Azure AD B2C 审核日志](view-audit-logs.md)。
