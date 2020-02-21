---
title: 用户迁移方法
titleSuffix: Azure AD B2C
description: 使用大容量导入或无缝迁移方法，将其他标识提供程序中的用户帐户迁移到 Azure AD B2C。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 759379d99bbd422c43998997be1d52ecbf34966d
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484276"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>将用户迁移到 Azure AD B2C

从另一个标识提供程序迁移到 Azure Active Directory B2C （Azure AD B2C）可能还需要迁移现有的用户帐户。 这里讨论了两种迁移方法：*大容量导入*和*无缝迁移*。 无论采用哪种方法，都需要编写一个应用程序或脚本，该应用程序或脚本使用[MICROSOFT GRAPH API](manage-user-accounts-graph-api.md)在 Azure AD B2C 中创建用户帐户。

## <a name="bulk-import"></a>大容量导入

在大容量导入流中，迁移应用程序为每个用户帐户执行以下步骤：

1. 从旧的标识提供程序中读取用户帐户，包括其当前凭据（用户名和密码）。
1. 使用当前凭据在 Azure AD B2C 目录中创建相应的帐户。

在这两种情况下，请使用大容量导入流：

- 你有权访问用户的纯文本凭据（用户名和密码）。
- 凭据已加密，但你可以对其进行解密。

有关以编程方式创建用户帐户的信息，请参阅[管理 Azure AD B2C 具有 Microsoft Graph 的用户帐户](manage-user-accounts-graph-api.md)。

## <a name="seamless-migration"></a>无缝迁移

如果旧标识提供者中的纯文本密码无法访问，请使用无缝迁移流。 例如，在以下情况下：

- 密码以单向加密格式（例如，使用哈希函数）进行存储。
- 旧标识提供程序以无法访问的方式存储密码。 例如，标识提供者通过调用 web 服务来验证凭据。

无缝迁移流仍需要批量迁移用户帐户，但随后使用[自定义策略](restful-technical-profile.md)来查询[REST API](rest-api-claims-exchange-dotnet.md) （创建），以便在首次登录时设置每个用户的密码。

因此，无缝迁移流程有两个阶段：*大容量导入*和*设置凭据*。

### <a name="phase-1-bulk-import"></a>阶段1：大容量导入

1. 你的迁移应用程序从旧的标识提供者读取用户帐户。
1. 迁移应用程序在 Azure AD B2C 目录中创建相应的用户帐户，但不*设置密码*。

### <a name="phase-2-set-credentials"></a>阶段2：设置凭据

批量迁移帐户完成后，自定义策略和 REST API 会在用户登录时执行以下操作：

1. 读取与输入的电子邮件地址相对应的 Azure AD B2C 用户帐户。
1. 通过评估布尔值扩展属性来检查是否已将帐户标记为要进行迁移。
    - 如果扩展属性返回 `True`，则调用 REST API 以根据旧标识提供者来验证密码。
      - 如果 REST API 确定密码不正确，则向用户返回友好的错误消息。
      - 如果 REST API 确定密码是正确的，请将密码写入 Azure AD B2C 帐户并将布尔扩展属性更改为 "`False`"。
    - 如果布尔值扩展属性返回 `False`，则继续正常登录过程。

若要查看示例自定义策略和 REST API，请参阅 GitHub 上的[无缝用户迁移示例](https://aka.ms/b2c-account-seamless-migration)。

用户迁移的无缝迁移方法的 ![流程图](./media/user-migration/diagram-01-seamless-migration.png)<br />*关系图：无缝迁移流*

## <a name="best-practices"></a>最佳做法

### <a name="security"></a>安全性

无缝迁移方法使用你自己的自定义 REST API 来针对旧标识提供者验证用户的凭据。

**必须保护 REST API 免受暴力攻击。** 攻击者可以提交多个密码，以最终推测用户的凭据。 若要帮助应对此类攻击，请在登录尝试次数超过特定阈值时停止向 REST API 提供请求。 此外，使用[客户端证书](secure-rest-api-dotnet-certificate-auth.md)保护 Azure AD B2C 和 REST API 之间的通信。

### <a name="user-attributes"></a>用户属性

并非旧标识提供程序中的所有信息都应该迁移到 Azure AD B2C 目录。 确定在迁移之前要存储在 Azure AD B2C 中的一组适当的用户属性。

- 在 Azure AD B2C**中存储**
  - 用户名、密码、电子邮件地址、电话号码、成员身份号/标识符。
  - 隐私策略和最终用户许可协议的许可标记。
- **不**存储 Azure AD B2C
  - 敏感数据，如信用卡号、社会保障号（SSN）、医疗记录或由政府或行业符合性团体管控的其他数据。
  - 营销或通信首选项、用户行为和见解。

### <a name="directory-clean-up"></a>目录清理

在开始迁移过程之前，请有机会清理目录。

- 确定要存储在 Azure AD B2C 中的一组用户属性，并仅迁移所需的属性。 如有必要，您可以创建[自定义属性](custom-policy-custom-attributes.md)以存储有关用户的更多数据。
- 如果要从具有多个身份验证源的环境进行迁移（例如，每个应用程序都有其自己的用户目录），请迁移到 Azure AD B2C 中的统一帐户。
- 如果多个应用程序具有不同的用户名，则可以使用标识集合将其全部存储在 Azure AD B2C 用户帐户中。 对于密码，让用户选择一个密码，并在目录中进行设置。 例如，通过无缝迁移，只应在 Azure AD B2C 帐户中存储所选密码。
- 在迁移之前删除未使用的用户帐户，或者不迁移过时的帐户。

### <a name="password-policy"></a>密码策略

如果要迁移的帐户的密码强度比 Azure AD B2C 强制实施的[强密码强度](../active-directory/authentication/concept-sspr-policy.md)弱，则可以禁用强密码要求。 有关详细信息，请参阅[密码策略属性](manage-user-accounts-graph-api.md#password-policy-property)。

## <a name="next-steps"></a>后续步骤

GitHub 上的[azure ad-b2c/用户迁移](https://github.com/azure-ad-b2c/user-migration)存储库包含无缝迁移自定义策略示例，并 REST API 代码示例：

[无缝用户迁移自定义策略 & REST API 代码示例](https://aka.ms/b2c-account-seamless-migration)
