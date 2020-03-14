---
title: Azure AD B2C 的最佳实践
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C （Azure AD B2C）时要考虑的建议和最佳方案。
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: a76852a6e3cc5ffcdfcac62ce29fe47c97af3df1
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136153"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Azure Active Directory B2C 的建议和最佳实践

以下最佳实践和建议涵盖了将 Azure Active Directory （Azure AD） B2C 集成到现有或新应用程序环境中的一些主要方面。

## <a name="fundamentals"></a>基本

|  |  |
|--|--|
| 在大多数情况下选择用户流 | Azure AD B2C 的标识体验框架是服务的核心强度。 策略充分描述了标识体验，例如注册、登录或配置文件编辑。 为帮助您设置最常见的标识任务，Azure AD B2C 门户包括称为用户流的预定义的可配置策略。 使用用户流，只需单击几下鼠标，就能在几分钟内创建极佳的用户体验。 [了解何时使用用户流与自定义策略](custom-policy-overview.md#comparing-user-flows-and-custom-policies)。|
| 应用注册 | 必须在 Azure AD B2C 中注册每个要保护的应用程序（web、本机）和 API。 如果应用同时具有 iOS 和 Android 的 web 版本和本机版本，则可以使用相同的客户端 ID 在 Azure AD B2C 中将其注册为一个应用程序。 了解如何[注册 OIDC、SAML、web 和本机应用](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=applications)。 详细了解[可在 Azure AD B2C 中使用的应用程序类型](https://docs.microsoft.com/azure/active-directory-b2c/application-types)。 |
| 转到每月活动用户计费 | Azure AD B2C 已从每月活动用户（MAU）计费转移到每月活动的身份验证。 大多数客户会发现这种模型经济高效。 [详细了解每月活动用户计费](https://azure.microsoft.com/updates/mau-billing/)。 |

## <a name="planning-and-design"></a>规划和设计

定义应用程序和服务体系结构、列出当前系统清单，并计划迁移到 Azure AD B2C。

|  |  |
|--|--|
| 构建端到端解决方案 | 规划 Azure AD B2C 集成时，请包含所有应用程序的依赖关系。 请考虑当前在你的环境中或可能需要添加到解决方案中的所有服务和产品，例如 Azure Functions、客户关系管理（CRM）系统、Azure API 管理网关和存储服务。 考虑所有服务的安全性和可扩展性。 |
| 记录您的用户体验 | 详细说明你的客户在你的应用程序中可以体验的所有用户旅程。 当与应用程序的标识和配置文件部分交互时，包括每个屏幕和它们可能遇到的任何分支流。 在规划中包括可用性、可访问性和本地化。 |
| 选择正确的身份验证协议 |  若要详细了解不同的应用程序方案及其建议的身份验证流程，请参阅[方案和支持的身份验证流](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。 |
| 试验概念证明（POC）端到端用户体验 | 首先，我们的[Microsoft 代码示例](code-samples.md)和[社区示例](https://github.com/azure-ad-b2c/samples)。 |
| 创建迁移计划 |预先规划可以更顺利地进行迁移。 详细了解[用户迁移](user-migration.md)。|
| 可用性与安全性 | 你的解决方案必须在应用程序可用性与组织可接受的风险级别之间取得适当的平衡。 |
| 将本地依赖项移动到云 | 若要帮助确保弹性解决方案，请考虑将现有应用程序依赖项移到云中。 |
| 将现有应用迁移到 b2clogin.com | 弃用的 login.microsoftonline.com 将对2004年12月 2020 12 日的所有 Azure AD B2C 租户生效。 [了解详细信息](b2clogin.md)。 |

## <a name="implementation"></a>实现

在实现阶段，请考虑下列建议。

|  |  |
|--|--|
| 用 Visual Studio Code 的 Azure AD B2C 扩展编辑自定义策略 | [从 Visual Studio Code Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)下载 Visual Studio Code 和此社区构建的扩展。 尽管不是官方的 Microsoft 产品，Visual Studio Code 的 Azure AD B2C 扩展包含多项功能，可帮助简化自定义策略的使用。 |
| 了解如何排查 Azure AD B2C | 了解如何在开发期间对[自定义策略进行故障排除](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-custom-policies?tabs=applications)。 了解常规身份验证流的外观，并使用工具来发现异常和错误。 例如，使用[Application Insights](troubleshoot-with-application-insights.md)查看用户旅程的输出日志。 |
| 利用我们的经验证的自定义策略模式 | 查找多个增强型 Azure AD B2C 客户标识和访问管理（CIAM）用户旅程的[示例](https://github.com/azure-ad-b2c/samples)。 |


## <a name="testing"></a>测试

测试并自动化 Azure AD B2C 实现。

|  |  |
|--|--|
| 全局流量的帐户 | 使用不同全局地址的流量源来测试性能和本地化要求。 请确保所有 HTMLs、CSS 和依赖项都能满足你的性能需求。 |
| 功能和 UI 测试 | 测试端到端的用户流。 使用 Selenium、VS Web 测试等，每隔几分钟添加一次综合测试。 |
| 笔测试 | 在开始处理解决方案之前，执行渗透测试练习，验证所有组件是否安全，包括任何第三方依赖项。 验证是否已使用访问令牌保护 Api，并为应用程序方案使用正确的身份验证协议。 了解有关[渗透测试](https://docs.microsoft.com/azure/security/fundamentals/pen-testing)的详细信息，以及[Microsoft 云的统一渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)。 |
| A/B 测试 | 在推出你的整个总体之前，请使用一小部分随机用户来飞行你的新功能。 如果在 Azure AD B2C 中启用了 JavaScript，则可以与 Optimizely、清晰度等测试工具和其他工具集成。 |
| 负载测试 | Azure AD B2C 可以进行缩放，但你的应用程序只能在其所有依赖项都可以缩放时进行缩放。 对 Api 和 CDN 进行负载测试。 |
| 限制 |  如果在短时间内从同一源发送过多请求，Azure AD B2C 会限制流量。 在负载测试时使用多个流量源，并在应用程序中正常处理 `AADB2C90229` 错误代码。 |
| 自动化 | 使用持续集成和交付（CI/CD）管道自动执行测试和部署，例如[Azure DevOps](deploy-custom-policies-devops.md)。 |

## <a name="operations"></a>操作

管理你的 Azure AD B2C 环境。

|  |  |
|--|--|
| 创建多个环境 | 为了更轻松地执行操作和部署，为开发、测试、预生产和生产创建单独的环境。 为每个创建 Azure AD B2C 租户。 |
| 为自定义策略使用版本控制 | 请考虑为你的 Azure AD B2C 自定义策略使用 GitHub、Azure Repos 或其他基于云的版本控制系统。 |
| 使用 Microsoft Graph API 自动管理 B2C 租户 | Microsoft Graph Api：<br/>管理[标识体验框架](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta)（自定义策略）<br/>[“键”](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta)<br/>[用户流](https://docs.microsoft.com/graph/api/resources/identityuserflow?view=graph-rest-beta) |
| 与 Azure DevOps 集成 | [CI/CD 管道](deploy-custom-policies-devops.md)可在不同环境之间轻松移动代码，并确保在所有时间都能实现生产就绪。   |
| 与 Azure Monitor 集成 | [审核日志事件](view-audit-logs.md)只保留7天。 [与 Azure Monitor 集成](azure-monitor.md)，以保留长期使用的日志，或与第三方安全信息和事件管理（SIEM）工具集成，以深入了解环境。 |
| 设置活动警报和监视 | 使用 Application Insights 跟踪 Azure AD B2C 中的[用户行为](active-directory-b2c-custom-guide-eventlogger-appins.md)。 |


## <a name="support-and-status-updates"></a>支持和状态更新

随时了解服务的状态，并查找支持选项。

|  |  |
|--|--|
| [服务更新](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  随时了解 Azure AD B2C 的产品更新和公告。 |
| [Microsoft 支持](support-options.md) | 为 Azure AD B2C 技术问题提供支持请求。 计费和订阅管理支持免费提供。 |
| [Azure 状态](https://status.azure.com/status) | 查看所有 Azure 服务的当前运行状况状态。 |