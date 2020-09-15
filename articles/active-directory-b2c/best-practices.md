---
title: Azure AD B2C 最佳做法
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C (Azure AD B2C) 时考虑的建议和最佳做法。
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: c8739da859c00a9caf08ac833f7b4ae7ae52e392
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90084305"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>适用于 Azure Active Directory B2C 的建议和最佳做法

以下最佳做法和建议涵盖了将 Azure Active Directory (Azure AD) B2C 集成到现有或新的应用程序环境时的一些主要方面。

## <a name="fundamentals"></a>基本

| 最佳做法 | 说明 |
|--|--|
| 选择大多数方案的用户流 | Azure AD B2C 的 Identity Experience Framework 是该服务的核心优势。 策略充分描述了标识体验，例如注册、登录或配置文件编辑。 为了帮助你设置最常见的标识任务，Azure AD B2C 门户中提供了称作“用户流”的预定义可配置策略。 使用用户流，只需按几下鼠标就能快速创建极佳的用户体验。 [了解用户流与自定义策略的使用时机](custom-policy-overview.md#comparing-user-flows-and-custom-policies)。|
| 应用注册 | 必须在 Azure AD B2C 中注册每个要保护的应用程序（Web、本机）和 API。 如果应用具有 iOS 和 Android 的 Web 版与本机版，则你可以使用相同的客户端 ID 在 Azure AD B2C 中将其注册为一个应用程序。 了解如何[注册 OIDC、SAML、Web 和本机应用](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=applications)。 详细了解[可在 Azure AD B2C 中使用的应用程序类型](https://docs.microsoft.com/azure/active-directory-b2c/application-types)。 |
| 转移到月度活跃用户计费模式 | Azure AD B2C 已从月度活跃身份验证计费模式转移到月度活跃用户 (MAU) 计费模式。 大多数客户会发现这种模式更具性价比。 [详细了解月度活跃用户计费](https://azure.microsoft.com/updates/mau-billing/)。 |

## <a name="planning-and-design"></a>规划和设计

定义应用程序和服务体系结构、盘点当前系统，并计划迁移到 Azure AD B2C。

| 最佳做法 | 说明 |
|--|--|
| 构建端到端解决方案 | 规划 Azure AD B2C 集成时，请考虑到应用程序的所有依赖项。 考虑环境中当前存在的或者可能需要添加到解决方案的所有服务和产品，例如 Azure Functions、客户关系管理 (CRM) 系统、Azure API 管理网关和存储服务。 考虑所有服务的安全性和可伸缩性。 |
| 阐述用户体验 | 详细说明客户可以在应用程序中体验到的所有用户旅程。 包括客户在与应用程序的标识和配置文件交互时，可能会看到的每个屏幕和任何分支流。 在规划中包括可用性、可访问性和本地化。 |
| 选择适当的身份验证协议 |  有关不同应用程序方案的细节及其建议的身份验证流，请参阅[方案和支持的身份验证流](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。 |
| 试运行概念证明 (POC) 端到端用户体验 | 请从我们的 [Microsoft 代码示例](code-samples.md)和[社区示例](https://github.com/azure-ad-b2c/samples)着手。 |
| 创建迁移计划 |提前规划能够使迁移更顺利地进行。 详细了解[用户迁移](user-migration.md)。|
| 可用性与安全性 | 解决方案必须致力于在应用程序可用性与组织可接受的风险级别之间取得适当的平衡。 |
| 将本地依赖项转移到云中 | 为了帮助确保解决方案具有复原能力，请考虑将现有的应用程序依赖项转移到云中。 |
| 将现有应用迁移到 b2clogin.com | 弃用的 login.microsoftonline.com 将对2004年12月 2020 12 日的所有 Azure AD B2C 租户生效。 [了解详细信息](b2clogin.md)。 |
| 使用 Identity Protection 和条件访问 | 使用这些功能更好地控制有风险的身份验证和访问策略。 需要 Azure AD B2C 高级 P2。 [了解详细信息](conditional-access-identity-protection-overview.md)。 |

## <a name="implementation"></a>实现

在实施阶段，请考虑以下建议。

| 最佳做法 | 说明 |
|--|--|
| 使用 Visual Studio Code 的 Azure AD B2C 扩展编辑自定义策略 | 从 [Visual Studio Code 市场](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)下载 Visual Studio Code 以及社区开发的此扩展。 尽管 Visual Studio Code 的 Azure AD B2C 扩展不是 Microsoft 官方产品，但其中的多项功能可帮助简化自定义策略的处理。 |
| 了解如何排查 Azure AD B2C 问题 | 了解如何在开发期间[排查自定义策略问题](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-custom-policies?tabs=applications)。 了解常规身份验证流的大致形式，并使用相应的工具来发现异常和错误。 例如，使用 [Application Insights](troubleshoot-with-application-insights.md) 查看用户旅程的输出日志。 |
| 利用我们的经受验证的自定义策略模式库 | 查找多个增强型 Azure AD B2C 客户标识和访问管理 (CIAM) 用户旅程的[示例](https://github.com/azure-ad-b2c/samples)。 |

## <a name="testing"></a>测试

测试并自动化 Azure AD B2C 的实施。

| 最佳做法 | 说明 |
|--|--|
| 考虑全局流量 | 使用来自不同全局地址的流量源来测试性能和本地化要求。 确保所有 HTMLs、CSS 和依赖项符合性能需求。 |
| 功能和 UI 测试 | 测试端到端的用户流。 使用 Selenium、VS Web Test 等工具每隔几分钟添加一次综合测试。 |
| 渗透测试 | 在推出解决方案之前执行渗透测试演练，以验证所有组件（包括任何第三方依赖项）是否安全。 验证是否已使用访问令牌保护了 API，并为应用程序方案使用了适当的身份验证协议。 详细了解[渗透测试](https://docs.microsoft.com/azure/security/fundamentals/pen-testing)，以及 [Microsoft 云渗透测试统一参与规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)。 |
| A/B 测试 | 先在外部让少量的随机用户体验你的新功能，然后再将其推出到整个用户群。 在 Azure AD B2C 中启用 JavaScript 后，可与 Optimizely、Clarity 等 A/B 测试工具相集成。 |
| 负载测试 | Azure AD B2C 可以缩放，但应用程序仅在其所有依赖项均可缩放时才能缩放。 对 API 和 CDN 进行负载测试。 |
| 限制 |  如果在短时间内从同一个源发送了过多的请求，Azure AD B2C 会限制流量。 执行负载测试时请使用多个流量源，并在应用程序中适当处理 `AADB2C90229` 错误代码。 |
| 自动化 | 使用持续集成和交付 (CI/CD) 管道自动执行测试和部署，例如 [Azure DevOps](deploy-custom-policies-devops.md)。 |

## <a name="operations"></a>操作

管理 Azure AD B2C 环境。

| 最佳做法 | 说明 |
|--|--|
| 创建多个环境 | 为了更轻松地实施操作和部署，请创建单独的环境用于开发、测试、预生产和生产。 为每个环境创建 Azure AD B2C 租户。 |
| 对自定义策略使用版本控制 | 考虑对 Azure AD B2C 自定义策略使用 GitHub、Azure Repos 或其他基于云的版本控制系统。 |
| 使用 Microsoft Graph API 将 B2C 租户管理自动化 | Microsoft Graph API：<br/>管理 [Identity Experience Framework](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta&preserve-view=true)（自定义策略）<br/>[“键”](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta&preserve-view=true)<br/>[用户流](https://docs.microsoft.com/graph/api/resources/identityuserflow?view=graph-rest-beta&preserve-view=true) |
| 与 Azure DevOps 集成 | [CI/CD 管道](deploy-custom-policies-devops.md)可在不同环境之间轻松移动代码，并确保在所有时间都能实现生产就绪。   |
| 与 Azure Monitor 集成 | [审核日志事件](view-audit-logs.md) 只保留7天。 [与 Azure Monitor 集成](azure-monitor.md)，以保留日志供长期使用，或者将其与第三方安全信息和事件管理 (SIEM) 工具相集成，以获取有关环境的见解。 |
| 设置有效警报和监视 | 使用 Application Insights 跟踪 Azure AD B2C 中的[用户行为](active-directory-b2c-custom-guide-eventlogger-appins.md)。 |

## <a name="support-and-status-updates"></a>支持和状态更新

随时关注服务状态并查找支持选项。

| 最佳做法 | 说明 |
|--|--|
| [服务更新](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  随时关注 Azure AD B2C 产品更新和公告。 |
| [Microsoft 支持部门](support-options.md) | 遇到 Azure AD B2C 技术问题时请提出支持请求。 计费和订阅管理支持免费提供。 |
| [Azure 状态](https://status.azure.com/status) | 查看所有 Azure 服务的当前运行状况。 |
