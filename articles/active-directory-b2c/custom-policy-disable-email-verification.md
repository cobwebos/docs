---
title: 使用自定义策略在客户注册期间禁用电子邮件验证
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
ms.openlocfilehash: 29426f8e3797c89deb712e89e0d972dd1ac8028e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389303"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略在客户注册期间禁用电子邮件验证

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>先决条件

完成[自定义策略入门](custom-policy-get-started.md)中的步骤。 你应具有用于注册的工作自定义策略，并使用社交帐户和本地帐户进行登录。

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>将元数据添加到自断言技术配置文件

**LocalAccountSignUpWithLogonEmail**技术配置文件是[自行断言](self-asserted-technical-profile.md)的，它是在注册流程中调用的。 若要禁用电子邮件验证，请将 `EnforceEmailVerification` 元数据设置为 false。 覆盖扩展文件中的 LocalAccountSignUpWithLogonEmail 技术配置文件。 

1. 打开策略的扩展文件， 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkExtensions.xml`</em>。
1. 查找 `ClaimsProviders` 元素。 如果该元素不存在，请添加该元素。
1. 将以下声明提供程序添加到 `ClaimsProviders` 元素：

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>测试自定义策略

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 请确保使用包含 Azure AD 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD 租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册” 。
4. 选择“标识体验框架”。
5. 选择“上传自定义策略”，然后上传已更改的两个策略文件。****
2. 选择已上传的注册或登录策略，并单击“立即运行”按钮。
3. 你应该能够在不进行验证的情况下使用电子邮件地址进行注册。


## <a name="next-steps"></a>后续步骤

- 详细了解 IEF 参考中的[自断言技术配置文件](self-asserted-technical-profile.md)。
