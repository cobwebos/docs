---
title: 排查 Azure Active Directory B2C 中的自定义策略问题
description: 了解一些方法，用于解决使用 Azure Active Directory B2C 中的自定义策略时出现的错误。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4893025b7d54dad1f1da6c5967d3c1dec99b499b
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71826894"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Azure AD B2C 自定义策略和标识体验框架故障排除

如果使用 Azure Active Directory B2C (Azure AD B2C) 自定义策略，在以策略语言 XML 格式设置标识体验框架时可能会遇到一些难题。 学习如何编写自定义策略如同学习一种新语言。 本文介绍一些有助于发现和解决问题的工具与提示。

本文侧重于排查 Azure AD B2C 自定义策略配置问题， 而不是排查信赖方应用程序或其标识库问题。

## <a name="xml-editing"></a>XML 编辑

设置自定义策略时最常见的错误是设置了不正确的 XML 格式。 好的 XML 编辑器可以说是必不可少。 它会原生显示 XML、对内容进行色彩编码、预先填充常用字词、保留带索引的 XML 元素，并可以针对 XML 架构进行验证。

用户偏爱的两个编辑器是 [Visual Studio Code](https://code.visualstudio.com/) 和 [Notepad++](https://notepad-plus-plus.org/)。

XML 架构验证在上传 XML 文件之前会识别错误。 在[初学者包](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)的根文件夹中，获取 XML 架构定义文件 *TrustFrameworkPolicy_0.3.0.0.xsd*。 若要了解如何在编辑器中使用 XSD 架构文件进行验证，请在编辑器文档中查找“XML 工具”和“XML 验证”或类似内容。

用户可能会发现检查 XML 规则很有用。 Azure AD B2C 会拒绝检测到的任何 XML 格式错误。 格式不当的 XML 有时会导致出现误导性的错误消息。

## <a name="upload-policies-and-policy-validation"></a>上传策略和策略验证

上传时会自动执行 XML 策略文件的验证。 大多数错误会导致上传失败。 验证包括正在上传的策略文件。 它还包括上传文件引用的文件链（信赖方策略文件、扩展文件和基本文件）。

常见的验证错误包括：

> 错误代码片段：`...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* ClaimType 值可能拼写错误，或者在架构中不存在。
* 必须至少在策略中的一个文件内定义 ClaimType 值。
    例如： `<ClaimType Id="issuerUserId">`
* 如果 ClaimType 在扩展文件中定义，但同时用于基本文件中的 TechnicalProfile 值，则上传基本文件会导致错误。

> 错误代码片段：`...makes a reference to a ClaimsTransformation with id...`

* 导致该错误的原因可能与导致 ClaimType 错误的原因相同。

> 错误代码片段：`Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* 检查 `<TrustFrameworkPolicy\>` 和 `<BasePolicy\>` 元素中的 TenantId 值是否与目标 Azure AD B2C 租户匹配。

## <a name="troubleshoot-the-runtime"></a>运行时故障排除

* 使用“立即运行”和 `https://jwt.ms` 独立于 Web 应用程序或移动应用程序测试策略。 此网站的作用类似于信赖方应用程序。 它显示 Azure AD B2C 策略生成的 JSON web 令牌（JWT）的内容。

    若要创建可重定向到 `https://jwt.ms` 进行令牌检查的测试应用程序：

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* 若要跟踪客户端浏览器与 Azure AD B2C 之间的消息交换，请使用 [Fiddler](https://www.telerik.com/fiddler)。 通过它可以了解用户旅程在业务流程步骤中的哪个环节失败。

* 在“开发模式”下，使用 [Application Insights](active-directory-b2c-troubleshoot-custom.md) 跟踪标识体验框架用户旅程的活动。 在**开发模式**下，你可以观察标识体验框架和技术配置文件定义的各种声明提供程序（例如标识提供者、基于 API 的服务、Azure AD B2C 用户）之间的声明交换目录和其他服务，如 Azure 多重身份验证。

## <a name="recommended-practices"></a>建议的做法

**保留方案的多个版本。将这些版本连同应用程序一起分组到某个项目中。** 基本文件、扩展文件和信赖方文件直接相互依赖。 将它们保存为一个组。 保留单独的工作版本，因为会向策略添加新功能。 使用与工作版本交互的应用程序代码，将这些版本暂存在用户自己的文件系统中。 应用程序可能调用一个租户中的多个不同信赖方策略。 它们可能会开始依赖预期从 Azure AD B2C 策略获得的声明。

**使用已知的用户旅程开发并测试技术配置文件。** 使用经过测试的初学者包策略设置技术配置文件。 在合并到自己的用户旅程之前，单独对其进行测试。

**使用经过测试的技术配置文件开发并测试用户旅程。** 逐步更改用户旅程的业务流程步骤。 以渐进方式生成所需的方案。

## <a name="next-steps"></a>后续步骤

在 GitHub 中下载 [active-directory-b2c-custom-policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) .zip 存档。 也可以克隆该存储库：

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
