---
title: Azure AD B2C 的最佳实践
titleSuffix: Azure AD B2C
description: 使用 Azure 活动目录 B2C（Azure AD B2C）时要考虑的建议和最佳实践。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136153"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Azure 活动目录 B2C 的建议和最佳实践

以下最佳实践和建议涵盖了将 Azure 活动目录 （Azure AD） B2C 集成到现有或新应用程序环境中的一些主要方面。

## <a name="fundamentals"></a>基础知识

|  |  |
|--|--|
| 为大多数方案选择用户流 | Azure AD B2C 的标识体验框架是服务的核心优势。 策略充分描述了标识体验，例如注册、登录或配置文件编辑。 若要帮助设置最常见的标识任务，Azure AD B2C 门户应包括名为“用户流”的预定义且可配置的策略。 使用用户流，只需点击几下即可在几分钟内创建出色的用户体验。 [了解如何何时使用用户流与自定义策略](custom-policy-overview.md#comparing-user-flows-and-custom-policies)。|
| 应用注册 | 正在保护的每个应用程序（Web、本机）和 API 都必须在 Azure AD B2C 中注册。 如果应用同时具有 Web 和本机版本的 iOS 和 Android，则可以将它们注册为 Azure AD B2C 中的一个应用程序，并具有相同的客户端 ID。 了解如何注册[OIDC、SAML、Web 和本机应用](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=applications)。 详细了解可在[Azure AD B2C 中使用的应用程序类型](https://docs.microsoft.com/azure/active-directory-b2c/application-types)。 |
| 移动到每月活动用户计费 | Azure AD B2C 已经从每月活动身份验证移动到每月活动用户 （MAU） 计费。 大多数客户会发现这种模式是具有成本效益的。 [了解有关每月活跃用户计费的更多信息](https://azure.microsoft.com/updates/mau-billing/)。 |

## <a name="planning-and-design"></a>规划和设计

定义应用程序和服务体系结构、清点当前系统并计划迁移到 Azure AD B2C。

|  |  |
|--|--|
| 构建端到端解决方案 | 在规划 Azure AD B2C 集成时，包括应用程序的所有依赖项。 考虑当前环境中或可能需要添加到解决方案中的所有服务和产品，例如 Azure 函数、客户关系管理 （CRM） 系统、Azure API 管理网关和存储服务。 考虑所有服务的安全性和可扩展性。 |
| 记录用户体验 | 详细说明您的客户在应用程序中可以体验的所有用户旅程。 包括与应用程序的身份和配置文件方面交互时可能遇到的每个屏幕和任何分支流。 在规划中包括可用性、可访问性和本地化。 |
| 选择正确的身份验证协议 |  有关不同应用程序方案及其建议的身份验证流的细目，请参阅[方案和支持的身份验证流](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。 |
| 试用概念验证 （POC） 端到端用户体验 | 从我们的[微软代码示例](code-samples.md)[和社区示例](https://github.com/azure-ad-b2c/samples)开始。 |
| 创建迁移计划 |提前规划可以使迁移更加顺利。 了解有关[用户迁移](user-migration.md)的更多内容。|
| 可用性与安全性 | 您的解决方案必须在应用程序可用性与组织的可接受风险级别之间取得适当的平衡。 |
| 将本地依赖项移动到云 | 为了帮助确保有弹性的解决方案，请考虑将现有应用程序依赖项移动到云中。 |
| 将现有应用迁移到b2clogin.com | 2020 年 12 月 4 日，所有 Azure AD B2C 租户的 login.microsoftonline.com弃用生效。 [了解详情](b2clogin.md)。 |

## <a name="implementation"></a>实现

在实施阶段，请考虑以下建议。

|  |  |
|--|--|
| 使用 Azure AD B2C 扩展编辑可视化工作室代码的自定义策略 | [从可视化工作室代码市场](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)下载可视化工作室代码和这个社区构建的扩展。 虽然不是微软的官方产品，但 Visual Studio Code 的 Azure AD B2C 扩展包括几个功能，可帮助更轻松地使用自定义策略。 |
| 了解如何对 Azure AD B2C 进行故障排除 | 了解如何在开发期间[对自定义策略进行故障排除](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-custom-policies?tabs=applications)。 了解正常身份验证流的外观，并使用工具发现异常和错误。 例如，使用[应用程序见解](troubleshoot-with-application-insights.md)查看用户旅程的输出日志。 |
| 利用我们经过验证的自定义策略模式库 | 查找[多个](https://github.com/azure-ad-b2c/samples)增强的 Azure AD B2C 客户标识和访问管理 （CIAM） 用户旅程的示例。 |


## <a name="testing"></a>正在测试

测试并自动执行 Azure AD B2C 实现。

|  |  |
|--|--|
| 全球流量帐户 | 使用来自不同全局地址的流量源来测试性能和本地化要求。 确保所有 HTM、CSS 和依赖项都能满足您的性能需求。 |
| 功能和 UI 测试 | 端到端测试用户流。 使用硒、VS Web 测试等每隔几分钟添加一次合成测试。 |
| 笔测试 | 在使用解决方案之前，请执行渗透测试练习，以验证所有组件是否安全，包括任何第三方依赖关系。 验证您是否已使用访问令牌保护 API，并为应用程序方案使用正确的身份验证协议。 了解有关[渗透测试](https://docs.microsoft.com/azure/security/fundamentals/pen-testing)和[微软云统一渗透测试参与规则的更多。](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) |
| A/B 测试 | 使用少量随机用户飞行新功能，然后再向整个总体推出。 在 Azure AD B2C 中启用了 JavaScript 后，您可以与 A/B 测试工具（如优化、清晰度等）集成。 |
| 负载测试 | Azure AD B2C 可以缩放，但应用程序只有在其所有依赖项都可以缩放时才能扩展。 加载测试 API 和 CDN。 |
| 遏制 |  如果在短时间内从同一源发送的请求过多，Azure AD B2C 会限制流量。 在负载测试时使用多个流量源，并在应用程序中`AADB2C90229`正常处理错误代码。 |
| 自动化 | 使用连续集成和交付 （CI/CD） 管道来自动执行测试和部署，例如[Azure DevOps](deploy-custom-policies-devops.md)。 |

## <a name="operations"></a>操作

管理 Azure AD B2C 环境。

|  |  |
|--|--|
| 创建多个环境 | 为了更轻松地操作和部署部署部署，请为开发、测试、预生产和生产创建单独的环境。 为每个租户创建 Azure AD B2C 租户。 |
| 对自定义策略使用版本控制 | 请考虑为 Azure AD B2C 自定义策略使用 GitHub、Azure 存储库或其他基于云的版本控制系统。 |
| 使用 Microsoft 图形 API 自动管理 B2C 租户 | 微软图形 API：<br/>管理[身份体验框架](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta)（自定义策略）<br/>[密钥](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta)<br/>[用户流](https://docs.microsoft.com/graph/api/resources/identityuserflow?view=graph-rest-beta) |
| 与 Azure DevOps 集成 | [CI/CD 管道](deploy-custom-policies-devops.md)使在不同环境之间移动代码变得容易，并确保随时做好生产准备。   |
| 与 Azure Monitor 集成 | [审核日志事件](view-audit-logs.md)仅保留七天。 [与 Azure 监视器集成](azure-monitor.md)以保留日志以长期使用，或与第三方安全信息和事件管理 （SIEM） 工具集成，以深入了解您的环境。 |
| 设置活动警报和监视 | 使用应用程序见解跟踪 Azure AD B2C 中的[用户行为](active-directory-b2c-custom-guide-eventlogger-appins.md)。 |


## <a name="support-and-status-updates"></a>支持和状态更新

随时了解服务状态并查找支持选项。

|  |  |
|--|--|
| [服务更新](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  随时了解 Azure AD B2C 产品更新和公告。 |
| [微软支持](support-options.md) | 为 Azure AD B2C 技术问题提交支持请求。 计费和订阅管理支持免费提供。 |
| [Azure 状态](https://status.azure.com/status) | 查看所有 Azure 服务的当前运行状况状态。 |