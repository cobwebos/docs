---
title: 用户迁移方法
titleSuffix: Azure AD B2C
description: 通过使用批量导入或无缝迁移方法将用户帐户从其他标识提供程序迁移到 Azure AD B2C。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b3ee069985fd39288a562d3caafc50b12290c060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332337"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>将用户迁移到 Azure AD B2C

从另一个标识提供程序迁移到 Azure 活动目录 B2C（Azure AD B2C）可能还需要迁移现有用户帐户。 这里讨论了两种迁移方法，*即批量导入*和*无缝迁移*。 使用这两种方法，都需要编写一个应用程序或脚本，该应用程序或脚本使用[Microsoft 图形 API](manage-user-accounts-graph-api.md)在 Azure AD B2C 中创建用户帐户。

## <a name="bulk-import"></a>批量导入

在批量导入流中，迁移应用程序对每个用户帐户执行以下步骤：

1. 从旧标识提供程序读取用户帐户，包括其当前凭据（用户名和密码）。
1. 使用当前凭据在 Azure AD B2C 目录中创建相应的帐户。

在以下两种情况下使用批量导入流：

- 您可以访问用户的纯文本凭据（其用户名和密码）。
- 凭据已加密，但您可以解密它们。

有关以编程方式创建用户帐户的信息，请参阅[使用 Microsoft 图形管理 Azure AD B2C 用户帐户](manage-user-accounts-graph-api.md)。

## <a name="seamless-migration"></a>无缝迁移

如果无法访问旧标识提供程序中的纯文本密码，请使用无缝迁移流。 例如，在：

- 密码以单向加密格式存储，例如使用哈希函数。
- 密码由旧版标识提供程序以您无法访问的方式存储。 例如，当标识提供程序通过调用 Web 服务来验证凭据时。

无缝迁移流仍然需要用户帐户的批量迁移，但随后使用[自定义策略](custom-policy-get-started.md)查询[REST API（](custom-policy-rest-api-intro.md)您创建的）来在首次登录时设置每个用户的密码。

因此，无缝迁移流有两个阶段：*批量导入*和*设置凭据*。

### <a name="phase-1-bulk-import"></a>第 1 阶段：批量进口

1. 您的迁移应用程序从旧的标识提供程序读取用户帐户。
1. 迁移应用程序在 Azure AD B2C 目录中创建相应的用户帐户，但不会*设置密码*。

### <a name="phase-2-set-credentials"></a>第 2 阶段：设置凭据

完成帐户批量迁移后，自定义策略和 REST API 然后在用户登录时执行以下操作：

1. 阅读与输入的电子邮件地址对应的 Azure AD B2C 用户帐户。
1. 通过评估布尔扩展属性，检查帐户是否已标记为迁移。
    - 如果扩展属性返回`True`，请调用 REST API 以根据旧标识提供程序验证密码。
      - 如果 REST API 确定密码不正确，则向用户返回友好错误。
      - 如果 REST API 确定密码正确，请将密码写入 Azure AD B2C 帐户，并将布尔扩展属性`False`更改为 。
    - 如果布尔扩展属性返回`False`，则像往常一样继续登录过程。

要查看自定义策略和 REST API 的示例，请参阅 GitHub 上的[无缝用户迁移示例](https://aka.ms/b2c-account-seamless-migration)。

![无缝迁移方法的流程图图，用于用户迁移](./media/user-migration/diagram-01-seamless-migration.png)<br />*图表：无缝迁移流*

## <a name="best-practices"></a>最佳做法

### <a name="security"></a>安全性

无缝迁移方法使用您自己的自定义 REST API 来验证用户针对旧标识提供程序的凭据。

**您必须保护您的 REST API 免受暴力攻击。** 攻击者可以提交多个密码，希望最终猜测用户的凭据。 为了帮助击败此类攻击，当登录尝试数超过特定阈值时，停止向 REST API 提供请求。 此外，保护 Azure AD B2C 和 REST API 之间的通信。 要了解如何保护用于生产的 RESTful API，请参阅安全[RESTful API](secure-rest-api.md)。

### <a name="user-attributes"></a>用户属性

并非所有旧标识提供程序中的信息都应迁移到 Azure AD B2C 目录。 在迁移之前，确定要存储在 Azure AD B2C 中的相应用户属性集。

- **在**Azure AD B2C 中存储
  - 用户名、密码、电子邮件地址、电话号码、会员编号/标识符。
  - 隐私政策和最终用户许可协议的同意标记。
- **不要**存储在 Azure AD B2C 中
  - 敏感数据，如信用卡号、社会保险号 （SSN）、医疗记录或政府或行业合规机构监管的其他数据。
  - 营销或沟通偏好、用户行为和见解。

### <a name="directory-clean-up"></a>目录清理

在开始迁移过程之前，请借此机会清理目录。

- 标识要存储在 Azure AD B2C 中的用户属性集，并仅迁移所需的属性。 如有必要，您可以创建[自定义属性](custom-policy-custom-attributes.md)来存储有关用户的更多数据。
- 如果要从具有多个身份验证源的环境（例如，每个应用程序都有自己的用户目录）迁移到 Azure AD B2C 中的统一帐户。
- 如果多个应用程序具有不同的用户名，则可以使用标识集合将所有应用程序存储在 Azure AD B2C 用户帐户中。 关于密码，让用户选择一个并在目录中设置它。 例如，通过无缝迁移，应仅将所选密码存储在 Azure AD B2C 帐户中。
- 在迁移之前删除未使用的用户帐户，或者不迁移陈旧的帐户。

### <a name="password-policy"></a>密码策略

如果要迁移的帐户的密码强度低于 Azure AD B2C 强制[的强密码强度](../active-directory/authentication/concept-sspr-policy.md)，则可以禁用强密码要求。 有关详细信息，请参阅[密码策略属性](manage-user-accounts-graph-api.md#password-policy-property)。

## <a name="next-steps"></a>后续步骤

GitHub 上的[azure ad-b2c/用户迁移](https://github.com/azure-ad-b2c/user-migration)存储库包含无缝迁移自定义策略示例和 REST API 代码示例：

[无缝用户迁移自定义策略& REST API 代码示例](https://aka.ms/b2c-account-seamless-migration)
