---
title: Azure AD B2C 中的标识保护和条件访问
description: 了解如何使用标识保护洞察风险登录和风险检测。 了解如何使用条件访问，根据 Azure AD B2C 租户中的风险事件强制实施组织策略。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 921d9e2138c8aa9c09535a673a7cd2d32e9cddad
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "89270641"
---
# <a name="identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Azure AD B2C 的标识保护和条件访问

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

使用 Azure AD 标识保护和条件访问增强 Azure Active Directory B2C (Azure AD B2C) 的安全性。 标识保护风险检测功能会自动检测风险用户和风险登录并将其显示在 Azure AD B2C 租户中。 你可以创建使用这些风险检测功能的条件访问策略来确定操作并强制实施组织策略。 这些功能相辅相成，使 Azure AD B2C 应用程序所有者能够更好地控制风险身份验证和访问策略。
  
如果你已熟悉 Azure AD 中的[标识保护](../active-directory/identity-protection/overview-identity-protection.md)和[条件访问](../active-directory/conditional-access/overview.md)，那么在 Azure AD B2C 中使用这些功能时，获得的还是熟悉的体验，其中只存在本文中讨论的细微差别。

![B2C 租户中的条件访问](media/conditional-access-identity-protection-overview/conditional-access-b2c.png)

> [!NOTE]
> 若要使用条件访问，需要 Azure AD B2C 高级 P2。

## <a name="benefits-of-identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Azure AD B2C 的标识保护和条件访问的优点  

通过将条件访问策略与标识保护风险检测搭配使用，你可以使用相应的策略操作对风险身份验证进行响应。

- **让你对应用和客户群身份验证风险的洞察更上一层楼**。 利用 Azure AD 和 Microsoft 帐户每月数十亿次身份验证的信号，风险检测算法现在会针对本地使用者或公民身份验证，将身份验证标记为低、中、高风险。
- **通过配置你自己的自适应身份验证来自动解决风险问题**。 对于指定的应用程序，你可以要求一组特定的用户提供第二重身份验证，就像在多重身份验证 (MFA) 中一样。 也可以根据检测到的风险级别阻止访问。 与其他 Azure AD B2C 体验一样，你可以根据组织的理念、风格和品牌自定义最终用户体验。 如果用户无法获取访问权限，你还可以显示缓解方法。
- **根据位置、组和应用控制访问权限**。  条件访问还可用于控制基于非风险的情况。 例如，你可以要求访问特定应用的客户进行 MFA，或阻止来自特定地理位置的访问。
- **与 Azure AD B2C 用户流和 Identity Experience Framework 自定义策略集成**。 使用现有的自定义体验，并添加需要使用条件访问进行交互的控件。 你还可以实现用于授予访问权限的高级场景，例如基于知识的访问或你自己首选的 MFA 提供商。

## <a name="feature-differences-and-limitations"></a>功能差异和限制

Azure AD B2C 中的标识保护和条件访问通常与 Azure AD 中的工作方式相同，不同之处在于：

- 安全中心在 Azure AD B2C 中不可用。

- Azure AD B2C 租户中的 ROPC 服务器到服务器流不支持标识保护和条件访问。

- 在 Azure AD B2C 租户中，标识保护风险检测仅适用于本地 B2C 帐户，不适用于 Google 或 Facebook 等社交标识。

- 在 Azure AD B2C 租户中，可以使用一部分标识保护风险检测功能。 请参阅[设置标识保护](conditional-access-identity-protection-setup.md#set-up-identity-protection)。

- Azure AD B2C 租户中的条件访问设备合规性功能不可用。


## <a name="integrate-conditional-access-with-user-flows-and-custom-policies"></a>将条件访问与用户流和自定义策略集成

在 Azure AD B2C 中，你可以触发内置用户流中的条件访问条件。 还可以将条件访问纳入自定义策略中。 与 B2C 用户流的其他方面一样，你可以根据组织的理念、品牌和其他缓解方法自定义最终用户体验消息。 请参阅[定义条件访问技术配置文件](conditional-access-technical-profile.md)。

## <a name="microsoft-graph-api"></a>Microsoft Graph API

还可以使用 Microsoft Graph API 来管理 Azure AD B2C 中的条件访问策略。 如需了解详情，请参阅[条件访问文档](../active-directory/conditional-access/overview.md)和 [Microsoft Graph 参考](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta.md)。

## <a name="next-steps"></a>后续步骤

- [为 Azure AD B2C 设置标识保护和条件访问](conditional-access-identity-protection-setup.md)
- [了解 Azure AD 中的标识保护](../active-directory/identity-protection/overview-identity-protection.md)
- [了解条件访问](../active-directory/conditional-access/overview.md)
