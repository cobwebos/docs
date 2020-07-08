---
title: 客户注册期间禁用电子邮件验证
titleSuffix: Azure AD B2C
description: 了解客户在 Azure Active Directory B2C 中注册期间如何禁用电子邮件验证。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10613bd2d6219272248f882e45ae1c33d2cc9d61
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85384203"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>客户在 Azure Active Directory B2C 中注册期间禁用电子邮件验证

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

请按照以下步骤禁用电子邮件验证：

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 使用顶部菜单中的“目录 + 订阅”  筛选器来选择包含 Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”  。 或者，选择“所有服务”  并搜索并选择“Azure AD B2C”  。
1. 选择“用户流”  。
1. 选择要禁用电子邮件验证的用户流。 例如，*B2C_1_signinsignup*。
1. 选择“页面布局”。 
1. 选择“本地帐户注册页”  。
1. 在**用户属性**下，选择“电子邮件地址”  。
1. 在“要求验证”  下拉列表中，选择“否”  。
1. 选择“保存”。  现在已为此用户流禁用电子邮件验证。

## <a name="next-steps"></a>后续步骤

- 了解如何[自定义 Azure Active Directory B2C 中的用户界面](customize-ui-overview.md)

