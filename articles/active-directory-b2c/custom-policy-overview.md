---
title: Azure Active Directory B2C 自定义策略 | Microsoft Docs
description: 了解 Azure Active Directory B2C 自定义策略。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f72aedb010301f9c7b12778432c4f10feb10f7a3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246040"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的自定义策略

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

自定义策略是定义 Azure Active Directory B2C （Azure AD B2C）租户行为的配置文件。 用户流在 Azure AD B2C 门户中预定义，用于最常见的标识任务。 标识开发人员可以完全编辑自定义策略来完成许多不同的任务。

## <a name="comparing-user-flows-and-custom-policies"></a>比较用户流和自定义策略

| | 用户流 | 自定义策略 |
|-|-------------------|-----------------|
| 目标用户 | 具有或不具有标识专业知识的所有应用程序开发人员。 | 标识专业人员、系统集成人员、顾问和内部标识团队。 它们熟悉 OpenID Connect 流并了解标识提供者和基于声明的身份验证。 |
| 配置方法 | 具有用户友好用户界面 (UI) 的 Azure 门户。 | 直接编辑 XML 文件，并上传到 Azure 门户。 |
| UI 自定义 | 完全 UI 自定义，包括 HTML、CSS 和 JavaScript。<br><br>使用自定义字符串实现多语言支持。 | 相同 |
| 属性自定义 | 标准和自定义属性。 | 相同 |
| 令牌和会话管理 | 自定义令牌和多个会话选项。 | 相同 |
| 标识提供者 | 预定义的本地或社交提供程序以及大多数 OIDC 标识提供程序，如与 Azure Active Directory 租户的联合。 | 基于标准的 OIDC、OAUTH 和 SAML。  还可以通过结合使用与 REST Api 进行身份验证。 |
| 标识任务 | 使用本地帐户或许多社交帐户注册或登录。<br><br>自助密码重置。<br><br>配置文件编辑。<br><br>多重身份验证。<br><br>自定义令牌和会话。<br><br>访问令牌流。 | 使用自定义标识提供者或自定义范围完成与用户流相同的任务。<br><br>注册时在另一系统中预配用户帐户。<br><br>使用自己的电子邮件服务提供程序发送欢迎电子邮件。<br><br>使用 Azure AD B2C 外部的用户存储。<br><br>使用 API 通过受信任的系统验证用户提供的信息。 |

## <a name="policy-files"></a>策略文件

使用以下三种类型的策略文件：

- 基本文件 - 包含大多数定义。 建议对此文件进行极少量的更改，以帮助进行故障排除和长期维护策略。
- 扩展文件保存租户的唯一配置更改。
- 信赖方 (RP) 文件注重单个任务的文件，由应用程序或服务（又称信赖方）直接调用。 每个唯一任务需要自身的 RP，根据品牌要求，该数字可能是“应用程序总数 x 用例总数”。

Azure AD B2C 中的用户流遵循上面描述的三文件模式，但开发人员只能看到 RP 文件，同时，Azure 门户会在后台对扩展文件进行更改。

## <a name="custom-policy-core-concepts"></a>自定义策略核心概念

Azure 中的客户标识和访问管理 (CIAM) 服务包括：

- 一个用户目录，可通过使用 Microsoft Graph 进行访问，并保存本地帐户和联合帐户的用户数据。
- 访问“标识体验框架”。此框架协调用户与实体之间的信任，并在两者之间传递声明，以完成标识或访问管理任务。
- 安全令牌服务 (STS)，颁发 ID 令牌、刷新令牌和访问令牌（以及等效的 SAML 断言），并对其进行验证以保护资源。

Azure AD B2C 依次与标识提供程序、用户、其他系统和本地用户目录交互，以完成标识任务。 例如，登录用户、注册新用户或重置密码。 “标识体验框架”和策略（亦称为“用户旅程”或“信任框架策略”）可建立多方信任并显式定义执行组件、操作、协议和要完成的步骤顺序。

标识体验框架是一个完全可配置的、策略驱动的、基于云的 Azure 平台，用于协调标准协议格式（例如 OpenID Connect、OAuth、SAML）和几个非标准的实体之间的信任，例如 REST基于 API 的系统间声明交换。 该框架创建支持 HTML 和 CSS 的用户友好的白标体验。

自定义策略以一个或多个采用 XML 格式的文件表示，这些文件在分层链中相互引用。 XML 元素定义声明架构、声明转换、内容定义、声明提供程序、技术配置文件、用户旅程业务流程步骤，以及其他元素。 自定义策略可作为一个或多个 XML 文件进行访问，这些文件在信赖方调用时由标识体验框架执行。 配置自定义策略的开发人员必须严谨地定义信任关系，以包含元数据终结点和确切的声明交换定义，并配置每个标识提供者所需的机密、密钥和证书。

### <a name="inheritance-model"></a>继承模型

当应用程序调用 RP 策略文件时，Azure AD B2C 中的标识体验框架依次从基本文件、扩展文件和 RP 策略文件中添加所有元素，以组合当前生效的策略。  RP 文件中具有相同类型和名称的元素将替代扩展中的这些元素，扩展将替代基本。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [自定义策略入门](custom-policy-get-started.md)
