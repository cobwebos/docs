---
title: 在客户注册过程中禁用电子邮件验证
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中的客户注册过程中禁用电子邮件验证。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 369b4e13baa344a71a51b358ef810d1a66b4b6ae
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126717"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中的客户注册期间禁用电子邮件验证

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

请按照以下步骤禁用电子邮件验证：

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 使用顶部菜单中的 "**目录 + 订阅**" 筛选器选择包含 Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 选择“用户流”。
1. 选择要禁用电子邮件验证的用户流。 例如， *B2C_1_signinsignup*。
1. 选择**页面布局**。
1. 选择 "**本地帐户注册页**"。
1. 在 "**用户属性**" 下，选择 "**电子邮件地址**"。
1. 在 "**需要验证**" 下拉顺序中，选择 "**否**"。
1. 选择“保存”。 现在已为此用户流禁用电子邮件验证。

## <a name="next-steps"></a>后续步骤

- 了解如何[自定义中的用户界面 Azure Active Directory B2C](customize-ui-overview.md)

