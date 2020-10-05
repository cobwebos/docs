---
title: Azure AD B2C 的 ISV 合作伙伴库
titleSuffix: Azure AD B2C
description: 了解如何与 ISV 合作伙伴集成，以根据你的需求定制你的最终用户体验。 我们的合作伙伴网络扩展了我们的解决方案功能;启用 MFA、安全的客户身份验证、基于角色的访问控制;通过身份验证验证来对付欺诈行为。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9e83fcd6a8e6a7b0c81f47cc3ad97b7f55da1b3b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713179"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Azure Active Directory B2C ISV 合作伙伴

我们的 ISV 合作伙伴网络扩展了我们的解决方案功能，可帮助你构建无缝的最终用户体验。 使用 Azure AD B2C，你可以与 ISV 合作伙伴集成，以启用多重身份验证方法，启用安全的客户身份验证 (SCA) ，执行基于角色的访问控制，并通过身份验证和验证来抵御欺诈行为。 使用我们的详细示例演练来了解如何将应用集成到下面列出的 ISV 合作伙伴。

>[!NOTE]
>[GitHub 上的 Azure Active Directory B2C 社区网站](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/)还提供了社区提供的示例自定义策略。

## <a name="integration-isv-partners"></a>集成 ISV 合作伙伴

| ISV 合作伙伴 | 说明和集成演练  |
| :--- | :--- |
| ![徽标](./media/partner-gallery/arkose-logo.png) | [Arkose 实验室](./partner-arkose-labs.md) 是一种欺诈防御解决方案提供商，可帮助组织防范机器人攻击、帐户接管攻击和欺诈性帐户。
| ![徽标](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) 是一种身份验证和校对提供程序，它根据用户属性执行风险评估，以防止欺诈。|
| ![徽标](./media/partner-gallery/hypr-logo.png) | [HYPR](./partner-hypr.md) 是真正的无密码身份验证提供程序，它将密码替换为公钥加密，消除了欺诈、仿冒和凭据重用。|
| ![徽标](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md) 是具有 ID 验证解决方案、欺诈防御解决方案、合规性解决方案和其他信息的身份验证和校对提供程序。|
| ![徽标](./media/partner-gallery/itsme-logo.png) | [itsme](./partner-itsme.md) 是一种电子识别、身份验证和信任服务 (eiDAS) 相容的数字标识解决方案，允许用户安全登录，无需使用智能卡读卡器、密码、双因素身份验证和多个 PIN 代码。 |
| ![徽标](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) 是一种 ID 验证服务，用于启用实时自动 ID 验证，从而保护客户数据。 |
| ![徽标](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) 是一种分析和身份验证提供程序，用于验证用户标识，并根据用户设备提供全面的风险评估。 |
| ![徽标](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md) 是一个文档 ID 和面部生物识别验证解决方案，可让公司实时 *了解你的客户* 和标识要求。  |
| ![徽标](./media/partner-gallery/saviynt-logo.png) | [Saviynt](./partner-saviynt.md) 云-本机平台使用智能分析，提供跨应用程序集成，以简化 IT 现代化，提高安全性、治理和合规性。 业务现代化改变了公司查看访问请求、执行访问评审、报告分析和简化认证活动的方式。  |
| ![徽标](./media/partner-gallery/trusona-logo.png) | [Trusona](./partner-trusona.md) 集成可帮助你安全登录并启用无密码 authentication、多重身份验证和数字许可证扫描。|
| ![徽标](./media/partner-gallery/twilio-logo.png) | [Twilio 验证应用程序](./partner-twilio.md) 提供多个解决方案，用于启用多重身份验证 (MFA) 通过 SMS 一次性密码 (OTP) 、基于时间的一次性密码 (TOTP) 和推送通知，以及符合支付服务指令 2 (PSD2) 的 SCA 要求。|
| ![徽标](./media/partner-gallery/typingdna-logo.png) | [TypingDNA](./partner-typingdna.md) 是基于用户键入模式的身份验证和验证提供程序，它提供了强制执行多重身份验证的 ID 验证解决方案，有助于满足支付服务指令 2 (PSD2) 的 SCA 要求。 |
| ![徽标](./media/partner-gallery/whoiam-logo.png) | [WhoIAM](./partner-whoiam.md) 是一种署名的标识管理系统 (BRIMS) 应用程序，使组织能够通过语音、短信和电子邮件来验证其用户群。 

## <a name="next-steps"></a>后续步骤

在上表中选择一个合作伙伴，了解如何将其解决方案与 Azure AD B2C 集成。

其他信息：

- [GitHub 上的 B2C 社区网站](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/)
- [Azure AD B2C 上的自定义策略](custom-policy-overview.md)
