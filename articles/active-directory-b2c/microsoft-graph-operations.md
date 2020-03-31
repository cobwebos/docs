---
title: 支持的微软图形操作
titleSuffix: Azure AD B2C
description: 支持用于管理 Azure AD B2C 资源的 Microsoft 图形操作的索引，包括用户、用户流、标识提供程序、自定义策略、策略键等。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 27fe1a41365d96a4179f8c659b63dc22c7b9fc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184242"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>适用于 Azure AD B2C 的微软图形操作

以下 Microsoft 图形 API 操作支持用于管理 Azure AD B2C 资源，包括用户、标识提供程序、用户流、自定义策略和策略密钥。

以下各节中的每个链接都针对该操作的 Microsoft 图形 API 引用中的相应页面。

## <a name="user-management"></a>用户管理

- [列出用户](https://docs.microsoft.com/graph/api/user-list)
- [创建使用者用户](https://docs.microsoft.com/graph/api/user-post-users)
- [获取用户](https://docs.microsoft.com/graph/api/user-get)
- [更新用户](https://docs.microsoft.com/graph/api/user-update)
- [删除用户](https://docs.microsoft.com/graph/api/user-delete)

有关使用 Microsoft 图形 API 管理 Azure AD B2C 用户帐户的详细信息，请参阅[使用 Microsoft 图形管理 Azure AD B2C 用户帐户](manage-user-accounts-graph-api.md)。

## <a name="identity-providers-user-flow"></a>标识提供程序（用户流）

管理 Azure AD B2C 租户中可供用户流使用的身份提供程序。

- [列出在 Azure AD B2C 租户中注册的标识提供程序](https://docs.microsoft.com/graph/api/identityprovider-list)
- [创建标识提供程序](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [获取标识提供程序](https://docs.microsoft.com/graph/api/identityprovider-get)
- [更新标识提供程序](https://docs.microsoft.com/graph/api/identityprovider-update)
- [删除标识提供程序](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>用户流

为注册、登录、合并注册和登录、密码重置和配置文件更新配置预构建的策略。

- [列出用户流](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [创建用户流](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [获取用户流](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [删除用户流](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>自定义策略

以下操作允许您管理 Azure AD B2C 信任框架策略（称为[自定义策略](custom-policy-overview.md)）。

- [列出租户中配置的所有信任框架策略](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [创建信任框架策略](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [读取现有信任框架策略的属性](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [更新或创建信任框架策略。](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [删除现有信任框架策略](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>策略键

身份体验框架存储自定义策略中引用的机密，以在组件之间建立信任。 这些机密可以是对称或非对称键/值。 在 Azure 门户中，这些实体显示为**策略键**。

Microsoft 图形 API 中策略键的顶级资源是[受信任的框架密钥集](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset)。 每个**密钥集**至少包含一个**密钥**。 要创建键，请先创建一个空键集，然后在密钥集中生成一个键。 您可以创建手动密钥、上载证书或 PKCS12 密钥。 密钥可以是生成的机密、您定义的字符串（如 Facebook 应用程序密钥）或您上传的证书。 如果密钥集有多个键，则只有一个密钥处于活动状态。

### <a name="trust-framework-policy-keyset"></a>信任框架策略密钥集

- [列出信任框架密钥集](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [创建信任框架密钥集](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [获取密钥集](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [更新信任框架密钥集](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [删除信任框架密钥集](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>信任框架策略键

- [在密钥集中获取当前活动密钥](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [在密钥集中生成密钥](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [上传基于字符串的机密](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [上传 X.509 证书](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [上传 PKCS12 格式证书](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>应用程序

- [列出应用程序](https://docs.microsoft.com/graph/api/application-list)
- [创建应用程序](https://docs.microsoft.com/graph/api/resources/application)
- [更新应用程序](https://docs.microsoft.com/graph/api/application-update)
- [创建服务主体](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [创建 auth2 权限授予](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [删除应用程序](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>应用程序扩展属性

- [列出扩展属性](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C 提供一个目录，其中可以保存每个用户的 100 个自定义属性。 对于用户流，这些扩展属性[使用 Azure 门户进行管理](custom-policy-custom-attributes.md)。 对于自定义策略，Azure AD B2C 会在策略首次向扩展属性写入值时为您创建该属性。

## <a name="audit-logs"></a>审核日志

- [列出审核日志](https://docs.microsoft.com/graph/api/directoryaudit-list)

有关使用 Microsoft 图形 API 访问 Azure AD B2C 审核日志的详细信息，请参阅[访问 Azure AD B2C 审核日志](view-audit-logs.md)。
