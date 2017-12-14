---
title: "Azure Active Directory B2C：自定义策略 | Microsoft Docs"
description: "有关 Azure Active Directory B2C 自定义策略的主题"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 1ff398a4-2079-4615-94f1-57de22c0aad6
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.openlocfilehash: 6c59075bb1eacb05599b23be3d8731fa40eabf98
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C：自定义策略

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>什么是自定义策略？

自定义策略是定义 Azure AD B2C 租户行为的配置文件。 **内置策略**是在 Azure AD B2C 门户中为最常见标识任务预定义的，而标识开发人员可以全面编辑自定义策略，以完成数量几乎不受限制的任务。 请继续阅读，确定自定义策略是否适合你以及标识方案。

## <a name="comparing-built-in-policies-and-custom-policies"></a>内置策略和自定义策略的比较

| | 内置策略 | 自定义策略 |
|-|-------------------|-----------------|
|目标用户 | 具有或不具有标识专业知识的所有应用开发人员 | 标识专业人员：系统集成人员、顾问和内部标识团队。 他们能够熟悉运作 OpenIDConnect 流，并了解标识提供者和基于声明的身份验证 |
|配置方法 | 具有用户友好 UI 的 Azure 门户 | 直接编辑 XML 文件，并上传到 Azure 门户 |
|UI 自定义 | 完全 UI 自定义，包括 HTML、CSS 和 javascript 支持（需要自定义域）<br><br>使用自定义字符串实现多语言支持 | 相同 |
| 属性自定义 | 标准和自定义属性 | 相同 |
|令牌和会话管理 | 自定义令牌和多个会话选项 | 相同 |
|标识提供者| **目前**：预定义的本地社交提供程序<br><br>**将来**：基于标准的 OIDC、SAML、OAuth | **目前**：基于标准的 OIDC、OAUTH、SAML<br><br>**将来**：WsFed |
|标识任务（示例） | 使用本地帐户和许多社交帐户注册或登录<br><br>自助密码重置<br><br>配置文件编辑<br><br>多重身份验证方案<br><br>自定义令牌和会话<br><br>访问令牌流 | 使用自定义标识提供者或自定义范围完成与内置策略相同的任务<br><br>注册时在另一系统中预配用户<br><br>使用自己的电子邮件服务提供程序发送欢迎电子邮件<br><br>使用 B2C 外部的用户存储<br><br>通过 API 使用受信任的系统验证用户提供的信息 |

## <a name="policy-files"></a>策略文件

自定义策略以一个或多个采用 XML 格式的文件表示，这些文件在分层链中相互引用。 XML 元素定义：声明架构、声明转换、内容定义、声明提供程序/技术配置文件、Userjourney 业务流程步骤，以及其他元素。

我们建议使用三种类型的策略文件：

- **BASE 文件**：包含大多数定义，Azure 为此提供了完整的示例。  我们建议对此文件进行极少量的更改，以帮助进行故障排除和长期维护策略
- **EXTensions 文件**：保存租户的唯一配置更改
- **信赖方 (RP) 文件**：注重单个任务的文件，由应用程序或服务（又称信赖方）直接调用。  有关详细信息，请阅读有关策略文件定义的文章。  每个唯一任务需要自身的 RP，根据品牌要求，该数字可能是“应用程序总数 x 用例总数”。


Azure AD B2C 中的内置策略遵循上面描述的 3 文件模式，但开发人员只能看到信赖方 (RP) 文件，同时，门户会在后台对 EXTenstions 文件进行更改。

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>使用自定义策略时应了解的核心概念

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

Azure 的客户标识和访问管理 (CIAM) 服务。 该服务包括：

1. 一个用户目录：采用可通过 Microsoft Graph访问的专用 Azure Active Directory 形式，保存本地帐户和联合帐户的用户数据 
2. 访问“标识体验框架”。此框架协调用户与实体之间的信任，并在两者之间传递声明，以完成标识/访问管理任务 
3. 安全令牌服务 (STS)：颁发 ID 令牌、刷新令牌和访问令牌（以及等效的 SAML 断言），并对其进行验证以保护资源。

Azure AD B2C 按顺序与标识提供者、用户、其他系统和本地用户目录交互，以实现标识任务（例如将用户登录、注册新用户、重置密码）。 将建立多方信任并执行这些步骤的基础平台称为“标识体验框架”。此框架采用策略（亦称为“用户旅程”或“信任框架策略”）显式定义执行组件、操作、协议和要完成的步骤顺序。

### <a name="identity-experience-framework"></a>标识体验框架

一个完全可配置的、策略驱动的、基于云的 Azure 平台，用于协调采用标准协议格式（例如 OpenIDConnect、OAuth、SAML、WSFed）的实体（主要是声明提供程序）与一些非标准实体（例如基于 REST API 的系统间声明交换）之间的信任关系。 I2E 创建支持 HTML、CSS 和 javascript 的用户友好的白标体验。  目前，标识体验框架只能在 Azure AD B2C 服务的上下文中使用，更适用于 CIAM 相关任务。

### <a name="built-in-policies"></a>内置策略

预定义的配置文件，用于指导 Azure AD B2C 的行为以执行最常用的标识任务（即用户注册、登录、密码重置）以及与其关系也在 Azure AD B2C 中预定义的受信任方（例如，Facebook 标识提供者、LinkedIn、Microsoft 帐户、Google 帐户）进行交互。  将来，内置策略还可能为企业领域中常见的标识提供者（例如 Azure Active Directory Premium、Active Directory/ADFS、Salesforce ID 提供者等）提供自定义。


### <a name="custom-policies"></a>自定义策略

在 Azure AD B2C 租户中定义标识体验框架行为的配置文件。 自定义策略可作为一个或多个 XML 文件（请参阅“策略文件定义”）进行访问，这些文件在信赖方（例如，应用程序）调用时由标识体验框架执行。 标识开发人员可以直接编辑自定义策略来完成数量几乎不受限制的任务。 配置自定义策略的开发人员必须严谨地定义信任关系，以包含元数据终结点和确切的声明交换定义，并配置每个标识提供者所需的机密、密钥和证书。

## <a name="policy-file-definitions-for-identity-experience-framework-trustframeworks"></a>标识体验框架信任框架的策略文件定义

### <a name="policy-files"></a>策略文件

自定义策略以一个或多个采用 XML 格式的文件表示，这些文件在分层链中相互引用。 XML 元素定义：声明架构、声明转换、内容定义、声明提供程序/技术配置文件、用户旅程业务流程步骤，以及其他元素。  我们建议使用三种类型的策略文件：

- **BASE 文件**包含大多数定义，Azure 为此提供了完整的示例。  我们建议对此文件进行极少量的更改，以帮助进行故障排除和长期维护策略
- **EXTensions 文件**：保存租户的唯一配置更改
- **信赖方 (RP) 文件**是注重单个任务的文件，由应用程序或服务（又称信赖方）直接调用。  有关详细信息，请阅读有关策略文件定义的文章。  每个唯一任务需要自身的 RP，根据品牌要求，该数字可能是“应用程序总数 x 用例总数”。

![策略文件类型](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| 策略文件的类型 | 示例文件名 | 建议用途 | 继承自 |
|---------------------|--------------------|-----------------|---------------|
| BASE |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | 包括 Microsoft 配置的核心声明架构、声明转换、声明提供程序和用户旅程<br><br>对此文件进行少量的更改 | 无 |
| 扩展 (EXT) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | 在此处将更改合并到 BASE 文件<br><br>修改的声明提供程序<br><br>修改的用户旅程<br><br>自己的自定义架构定义 | BASE 文件 |
| 信赖方 (RP) | B2C_1A_sign_up_sign_in.xml| 在此处更改令牌形状和会话设置| Extensions(EXT) 文件 |

### <a name="inheritance-model"></a>继承模型

当应用程序调用 RP 策略文件时，B2C 中的标识体验框架将依次从 BASE 文件、EXTENSIONS 文件和 RP 策略文件中添加所有元素，以组合当前生效的策略。  RP 文件中具有相同类型和名称的元素将替代 EXTENSIONS 中的这些元素，EXTENSIONS 将替代 BASE。

Azure AD B2C 中的**内置策略**遵循上面描述的 3 文件模式，但开发人员只能看到信赖方 (RP) 文件，同时，门户会在后台对 EXTenstions 文件进行更改。  整个 Azure AD B2C 共享受 Azure B2C 团队控制的、经常更新的 BASE 策略文件。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [自定义策略入门](active-directory-b2c-get-started-custom.md)
