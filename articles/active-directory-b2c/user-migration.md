---
title: 用户迁移方法
titleSuffix: Azure AD B2C
description: 使用预迁移或无缝迁移方法将另一标识提供者中的用户帐户迁移到 Azure AD B2C。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 60dff717fbd86fa83821575ac90c9dac36dbc4d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85383965"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>将用户迁移到 Azure AD B2C

从另一标识提供者迁移到 Azure Active Directory B2C (Azure AD B2C) 可能还需要迁移现有的用户帐户。 本文介绍两种迁移方法：预迁移和无缝迁移。  无论使用哪种方法，都需要编写一个应用程序或脚本，以使用 [Microsoft Graph API](manage-user-accounts-graph-api.md) 在 Azure AD B2C 中创建用户帐户。

## <a name="pre-migration"></a>预迁移

在预迁移流中，迁移应用程序将对每个用户帐户执行以下步骤：

1. 读取旧标识提供者中的用户帐户，包括其当前凭据（用户名和密码）。
1. 使用当前凭据在 Azure AD B2C 目录中创建相应的帐户。

对于以下两种情况，请使用预迁移流：

- 你有权访问用户的纯文本凭据（用户名和密码）。
- 凭据已加密，但可将其解密。

有关以编程方式创建用户帐户的信息，请参阅[使用 Microsoft Graph 管理 Azure AD B2C 用户帐户](manage-user-accounts-graph-api.md)。

## <a name="seamless-migration"></a>无缝迁移

如果无法访问旧标识提供者中的纯文本密码，请使用无缝迁移流。 例如，在以下情况下使用该流：

- 密码是以单向加密格式存储的（例如，使用哈希函数）。
- 旧式标识提供者以你无法访问的方式存储了密码。 例如，标识提供者通过调用 Web 服务来验证凭据。

无缝迁移流仍需要迁移用户帐户，但随后使用 [自定义策略](custom-policy-get-started.md) 来查询 [REST API](custom-policy-rest-api-intro.md) (创建) ，以便在首次登录时设置每个用户的密码。

因此，无缝迁移流包括两个阶段：预迁移和设置凭据。 

### <a name="phase-1-pre-migration"></a>阶段 1：预迁移

1. 迁移应用程序读取旧标识提供者中的用户帐户。
1. 迁移应用程序在 Azure AD B2C 目录中创建相应的用户帐户，但不设置密码。 

### <a name="phase-2-set-credentials"></a>阶段 2：设置凭据

将帐户预迁移完以后，自定义策略和 REST API 随后会在用户登录时执行以下操作：

1. 读取对应于所输入的电子邮件地址的 Azure AD B2C 用户帐户。
1. 通过评估某个布尔扩展属性来检查是否已将该帐户标记为待迁移。
    - 如果该扩展属性返回 `True`，则会调用 REST API 根据旧标识提供者验证密码。
      - 如果 REST API 确定密码不正确，则向用户返回友好的错误消息。
      - 如果 REST API 确定密码正确，则将密码写入 Azure AD B2C 帐户，并将该布尔扩展属性更改为 `False`。
    - 如果该布尔扩展属性返回 `False`，则像平时一样继续执行登录过程。

若要查看示例自定义策略和 REST API，请参阅 GitHub 上的[无缝用户迁移示例](https://aka.ms/b2c-account-seamless-migration)。

![用户无缝迁移方法的流程图](./media/user-migration/diagram-01-seamless-migration.png)<br />*示意图：无缝迁移流*

## <a name="best-practices"></a>最佳实践

### <a name="security"></a>安全性

无缝迁移方法使用你自己的自定义 REST API 根据旧标识提供者验证用户的凭据。

**必须保护 REST API，使其免遭暴力破解攻击。** 攻击者可能会提交多个密码，最终猜出用户的凭据。 为了帮助抵御此类攻击，请在登录尝试次数超过特定的阈值时，停止向 REST API 提供请求。 此外，请保护 Azure AD B2C 与 REST API 之间的通信。 若要了解如何保护用于生产的 RESTful API，请参阅[保护 RESTful API](secure-rest-api.md)。

### <a name="user-attributes"></a>用户属性

并非要将旧标识提供者中的所有信息都迁移到 Azure AD B2C 目录。 在迁移之前，请确定要存储在 Azure AD B2C 中的适当用户属性集。

- **要**存储在 Azure AD B2C 中
  - 用户名、密码、电子邮件地址、电话号码、会员号/标识符。
  - 隐私策略和最终用户许可协议的同意标记。
- **不要**存储在 Azure AD B2C 中
  - 敏感数据，例如信用卡号、社会安全号码 (SSN)、医疗记录，或其他由政府或行业法规实体管制的数据。
  - 营销或沟通偏好、用户行为和见解。

### <a name="directory-clean-up"></a>目录清理

在启动迁移过程之前，请借机清理目录。

- 确定要存储在 Azure AD B2C 中的用户属性集，仅迁移所需的属性。 如有必要，您可以创建 [自定义属性](custom-policy-custom-attributes.md) 以存储有关用户的更多数据。
- 如果从包含多个身份验证源的环境迁移（例如，每个应用程序具有自身的用户目录），请迁移到 Azure AD B2C 中的统一帐户。
- 如果多个应用程序具有不同的用户名，可以使用标识集合将其全部存储在 Azure AD B2C 用户帐户中。 对于密码，请让用户选择密码，并在目录中设置该密码。 例如，使用无缝迁移时，只应在 Azure AD B2C 帐户中存储所选的密码。
- 在迁移之前删除未使用的用户帐户，或者不迁移已过时的帐户。

### <a name="password-policy"></a>密码策略

如果与 Azure AD B2C 强制实施的[强密码强度](../active-directory/authentication/concept-sspr-policy.md)相比，所要迁移的帐户的密码强度更弱，你可以禁用强密码要求。 有关详细信息，请参阅[密码策略属性](manage-user-accounts-graph-api.md#password-policy-property)。

## <a name="next-steps"></a>后续步骤

GitHub 上的 [azure-ad-b2c/user-migration](https://github.com/azure-ad-b2c/user-migration) 存储库包含了无缝迁移自定义策略示例和 REST API 代码示例：

[无缝用户迁移自定义策略和 REST API 代码示例](https://aka.ms/b2c-account-seamless-migration)
