---
title: "Azure Active Directory B2C：自定义策略 | Microsoft Docs"
description: "有关 Azure Active Directory B2C 自定义策略的主题"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 1ff398a4-2079-4615-94f1-57de22c0aad6
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: ed82300211f54f39423c24039ca418fca9da94c3
ms.contentlocale: zh-cn
ms.lasthandoff: 05/01/2017


---
# <a name="azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C：自定义策略

## <a name="what-are-custom-policies"></a>什么是自定义策略？

自定义策略是定义 Azure AD B2C 租户行为的配置文件。 **内置策略**是在 Azure AD B2C 门户中为最常见标识任务预定义的，而标识开发人员可以全面编辑自定义策略，以完成数量几乎不受限制的任务。 请继续阅读，确定自定义策略是否适合你以及你的标识方案。

**并非所有人都可以编辑自定义策略。** 其学习曲线要求很高，入门时间较长，并且维护将来对自定义策略所做的更改也需要同等的专业知识。 在使用自定义策略之前，应该先根据方案仔细考虑内置策略。

## <a name="comparing-built-in-policies-and-custom-policies"></a>内置策略和自定义策略的比较

| | 内置策略 | 自定义策略 |
|-|-------------------|-----------------|
|目标用户 | 具有或不具有标识专业知识的所有应用开发人员 | 标识专业人员：系统集成人员、顾问和内部标识团队。 他们能够熟悉运作 OpenIDConnect 流，并了解标识提供者和基于声明的身份验证 |
|配置方法 | 具有用户友好 UI 的 Azure 门户 | 直接编辑 XML 文件，然后上传到 Azure 门户 |
|UI 自定义 | 完全 UI 自定义，包括 HTML、CSS 和 jscript 支持（需要自定义域）<br><br>使用自定义字符串实现多语言支持 | 相同 |
| 属性自定义 | 标准和自定义属性 | 相同 |
|令牌和会话管理 | 自定义令牌和多个会话选项 | 相同 |
|标识提供者| **目前**：预定义的本地社交提供程序<br><br>**将来**：基于标准的 OIDC、SAML、OAuth | **目前**：基于标准的 OIDC、OAUTH、SAML<br><br>**将来**：WsFed |
|标识任务（示例） | 使用本地帐户和许多社交帐户注册或登录<br><br>密码重置<br><br>配置文件编辑<br><br>多重身份验证方案<br><br>自定义令牌和会话<br><br>访问令牌流 | 使用自定义标识提供者或自定义范围完成与内置策略相同的任务<br><br>注册时在另一系统中预配用户<br><br>使用你自己的电子邮件服务提供程序发送欢迎电子邮件<br><br>使用 B2C 外部的用户存储<br><br>通过 API 使用受信任的系统验证用户提供的信息 |

## <a name="policy-files"></a>策略文件

自定义策略以一个或多个采用 XML 格式的文件表示，这些文件在分层链中相互引用。 XML 元素定义：声明架构、声明转换、内容定义、声明提供程序/技术配置文件、Userjourney 业务流程步骤，以及其他元素。

我们建议使用三种类型的策略文件：

- **BASE 文件**：包含大多数定义，Azure 为此提供了完整的示例。  我们建议对此文件进行极少量的更改，以帮助进行故障排除和长期维护策略
- **EXTensions 文件**：保存租户的唯一配置更改
- **信赖方 (RP) 文件**：注重单个任务的文件，由应用程序或服务（又称信赖方）直接调用。  有关详细信息，请阅读有关策略文件定义的文章。  每个唯一任务需要自身的 RP，根据品牌要求，该数字可能是“应用程序总数 x 用例总数”。


Azure AD B2C 中的内置策略遵循上面描述的 3 文件模式，但开发人员只能看到信赖方 (RP) 文件，同时，门户将在后台对 EXTenstions 文件进行更改。

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>使用自定义策略时应了解的核心概念

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

Azure 的客户标识和访问管理 (CIAM) 服务。 该服务包括：

1. 一个用户目录：采用可通过 Microsoft Graph访问的专用 Azure Active Directory 形式，保存本地帐户和联合帐户的用户数据 
2. 访问**标识体验引擎**。该引擎协调用户与实体之间的信任，并在两者之间传递声明，以完成标识/访问管理任务 
3. 安全令牌服务 (STS)：颁发 ID 令牌、刷新令牌和访问令牌（以及等效的 SAML 断言），并对其进行验证以保护资源。

Azure AD B2C 按顺序与标识提供者、用户、其他系统和本地用户目录交互，以实现标识任务（例如将用户登录、注册新用户、重置密码）。 建立多方信任并执行这些步骤的基础平台称为标识体验引擎；某个策略（也称为用户旅程或信任框架）显式定义执行组件、操作、协议和要完成的步骤序列。

### <a name="identity-experience-engine"></a>标识体验引擎

一个完全可配置的、策略驱动的、基于云的 Azure 平台，用于协调采用标准协议格式（例如 OpenIDConnect、OAuth、SAML、WSFed）的实体（主要是声明提供程序）与非标准实体（例如基于 REST API 的系统间声明交换）之间的信任关系。 I2E 创建支持 HTML、CSS 和 jscript 的用户友好的白标体验。  目前，标识体验引擎只能在 Azure AD B2C 服务的上下文中使用，并已针对 CIAM 相关的任务进行优化。

### <a name="built-in-policies"></a>内置策略

预定义的配置文件，引导 Azure AD B2C 的行为，以执行最常用的标识任务（例如 用户注册、登录、密码重置），以及与在 Azure AD B2C 中也预定义了关系的受信任方（例如 Facebook 标识提供者、LinkedIn、Microsoft 帐户、Google 帐户）交互。  将来，内置策略还可能为企业领域中常见的提供标识提供者（例如 Azure Active Directory Premium、Active Directory/ADFS、Salesforce ID 提供者等）提供自定义。


### <a name="custom-policies"></a>自定义策略

在 Azure AD B2C 租户中定义标识体验引擎行为的配置文件。 可以一个或多个 XML 文件（请参阅“策略文件定义”）的形式访问自定义策略，这些文件由信赖方（例如应用程序）调用的标识体验引擎执行。 标识开发人员可以直接编辑自定义策略来完成数量几乎不受限制的任务。 配置自定义策略的开发人员必须严谨地定义信任关系，以包含元数据终结点和确切的声明交换定义，并配置每个标识提供者所需的机密、密钥和证书。

### <a name="policy-files"></a>策略文件

自定义策略以一个或多个采用 XML 格式的文件表示，这些文件在分层链中相互引用。 XML 元素定义：声明架构、声明转换、内容定义、声明提供程序/技术配置文件、Userjourney 业务流程步骤，以及其他元素。 我们建议使用三种类型的策略文件：

- **BASE 文件**：包含大多数定义，Azure 为此提供了完整的示例。  我们建议对此文件进行极少量的更改，以帮助进行故障排除和长期维护策略
- **EXTensions 文件**：保存租户的唯一配置更改
- **信赖方 (RP) 文件**：注重单个任务的文件，由应用程序或服务（又称信赖方）直接调用。  有关详细信息，请阅读有关策略文件定义的文章。  每个唯一任务需要自身的 RP，根据品牌要求，该数字可能是“应用程序总数 x 用例总数”。

## <a name="policy-file-definitions-for-identity-experience-engine-trustframeworks"></a>标识体验引擎信任框架的策略文件定义

### <a name="policy-files"></a>策略文件

自定义策略以一个或多个采用 XML 格式的文件表示，这些文件在分层链中相互引用。 XML 元素定义：声明架构、声明转换、内容定义、声明提供程序/技术配置文件、Userjourney 业务流程步骤，以及其他元素。  我们建议使用三种类型的策略文件：

- **BASE 文件**：包含大多数定义，Azure 为此提供了完整的示例。  我们建议对此文件进行极少量的更改，以帮助进行故障排除和长期维护策略
- **EXTensions 文件**：保存租户的唯一配置更改
- **信赖方 (RP) 文件**：注重单个任务的文件，由应用程序或服务（又称信赖方）直接调用。  有关详细信息，请阅读有关策略文件定义的文章。  每个唯一任务需要自身的 RP，根据品牌要求，该数字可能是“应用程序总数 x 用例总数”。

![策略文件类型](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| 策略文件的类型 | 示例文件名 | 建议用途 | 继承自 |
|---------------------|--------------------|-----------------|---------------|
| BASE |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | 包括 Microsoft 配置的核心声明架构、声明转换、声明提供程序和用户旅程<br><br>对此文件进行少量的更改 | 无 |
| 扩展 (EXT) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | 在此处将更改合并到 BASE 文件<br><br>修改的声明提供程序<br><br>修改的用户旅程<br><br>你自己的自定义架构定义 | BASE 文件 |
| 信赖方 (RP) | | | 扩展文件 |

### <a name="inheritance-model"></a>继承模型

当应用程序调用 RP 策略文件时，B2C 中的标识体验引擎将依次从 BASE 文件、EXTENSIONS 文件和 RP 策略文件中添加所有元素，以组合当前生效的策略。  RP 文件中具有相同类型和名称的元素将替代 EXTENSIONS 中的这些元素，EXTENSIONS 将替代 BASE。

Azure AD B2C 中的**内置策略**遵循上面描述的 3 文件模式，但开发人员只能看到信赖方 (RP) 文件，同时，门户将在后台对 EXTenstions 文件进行更改。  整个 Azure AD B2C 共享受 Azure B2C 团队控制的、经常更新的 BASE 策略文件。

