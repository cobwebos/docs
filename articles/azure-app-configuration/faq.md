---
title: Azure 应用配置常见问题解答
description: 有关 Azure 应用配置的常见问题
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 25187fd055f40e8b32d840ead2a9c54882446b88
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348790"
---
# <a name="azure-app-configuration-faq"></a>Azure 应用配置常见问题解答

本文回答了有关 Azure 应用配置的常见问题。

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>应用配置与 Azure 密钥保管库有什么不同？

应用配置可帮助开发人员管理应用程序设置和控制功能可用性。 它旨在简化处理复杂配置数据的许多任务。

应用配置支持：

- 分层命名空间
- 标记
- 广泛的查询
- 批量检索
- 专业化管理业务
- 功能管理用户界面

应用配置是对密钥保管库的补充，在大多数应用程序部署中，应并排使用这两个库。

## <a name="should-i-store-secrets-in-app-configuration"></a>我应该在应用配置中存储机密吗？

尽管应用配置提供了强化的安全性，但密钥保管库仍然是存储应用程序机密的最佳位置。 密钥保管库提供硬件级加密、粒度访问策略和管理操作（如证书轮换）。

您可以创建应用配置值，这些值引用存储在密钥保管库中的机密。 有关详细信息，请参阅在[ASP.NET 核心应用中使用密钥保管库引用](./use-key-vault-references-dotnet-core.md)。

## <a name="does-app-configuration-encrypt-my-data"></a>应用配置是否加密我的数据？

是的。 应用配置加密它保留的所有密钥值，并加密网络通信。 密钥名称和标签用作检索配置数据的索引，并且未加密。

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>应用配置与 Azure 应用服务设置有什么不同？

Azure 应用服务允许您为每个应用服务实例定义应用设置。 这些设置作为环境变量传递给应用程序代码。 如果需要，可以将设置与特定部署槽相关联。 有关详细信息，请参阅[配置应用设置](/azure/app-service/configure-common#configure-app-settings)。

相反，Azure 应用配置允许您定义可在多个应用之间共享的设置。 这包括在应用服务中运行的应用以及其他平台。 应用程序代码通过 .NET 和 Java 的配置提供程序、通过 Azure SDK 或直接通过 REST API 访问这些设置。

您还可以在应用服务和应用配置之间导入和导出设置。 此功能允许您根据现有应用服务设置快速设置新的应用配置存储。 您还可以与依赖于应用服务设置的现有应用共享配置。

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>应用配置中存储的密钥和值是否有任何大小限制？

单个键值项的限制为 10 KB。

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>如何存储多个环境（测试、暂存、生产等）的配置？

您可以控制谁可以在每商店级别访问应用配置。 对需要不同权限的每个环境使用单独的存储。 此方法提供最佳的安全隔离。

如果不需要环境之间的安全隔离，则可以使用标签来区分配置值。 [使用标签为不同的环境启用不同的配置](./howto-labels-aspnet-core.md)提供了一个完整的示例。

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>使用应用配置的建议方法有哪些？

请参阅[最佳实践](./howto-best-practices.md)。

## <a name="how-much-does-app-configuration-cost"></a>应用配置费用是多少？

有两个定价层：

- 免费层
- 标准层。

如果在引入标准层之前创建了商店，则该商店会在一般可用性时自动移动到免费层。 您可以选择升级到标准层或保留在免费层上。

不能将商店从标准层降级到免费层。 您可以在 Free 层中创建新存储，然后将配置数据导入该存储。

## <a name="which-app-configuration-tier-should-i-use"></a>我应该使用哪个应用配置层？

两个应用配置层都提供核心功能，包括配置设置、功能标志、密钥保管库引用、基本管理操作、指标和日志。

以下是选择层的注意事项。

- **每个订阅的资源**：资源由单个配置存储组成。 每个订阅仅限于免费套餐中的一个配置存储。 订阅可以在标准层中具有无限数量的配置存储。
- **每个资源的存储**：在免费层中，每个配置存储限制为 10 MB 的存储。 在标准层中，每个配置存储最多可以使用 1 GB 的存储空间。
- **密钥历史记录**：应用配置存储对密钥所做的所有更改的历史记录。 在免费套餐中，此历史记录存储七天。 在标准层中，此历史记录存储 30 天。
- **每天的请求**：免费套餐商店每天限制为 1，000 个请求。 一旦存储达到 1，000 个请求，它将返回所有请求的 HTTP 状态代码 429，直到 UTC 午夜。

    对于标准层商店，每天的前 200，000 个请求包含在每日费用中。 其他请求被计费为超额。

- **服务级别协议**：标准层的 SLA 可用性为 99.9%。 免费层没有 SLA。
- **安全功能**：这两个层都包括基本安全功能，包括使用 Microsoft 管理的密钥进行加密、通过 HMAC 或 Azure 活动目录进行身份验证、RBAC 支持和托管标识。 标准层提供更高级的安全功能，包括专用链接支持和客户管理的密钥加密。
- **成本**：标准层商店每天收取使用费。 对于超过每日分配的请求，还收取超额费用。 使用免费套餐商店无需任何费用。

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>是否可以将商店从免费层升级到标准层？ 我可以将商店从标准层降级到免费层吗？

您可以随时从免费套餐升级到标准层。

不能将商店从标准层降级到免费层。 您可以在 Free 层中创建新存储，然后将[配置数据导入该存储](howto-import-export-data.md)。

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>向应用配置发出的请求数是否有任何限制？

免费套餐中的配置存储限制为每天 1，000 个请求。 当请求速率超过每小时 20，000 个请求时，标准层中的配置存储可能会遇到临时限制。

当存储达到其限制时，它将返回 HTTP 状态代码 429，用于在时间段到期之前发出的所有请求。 响应`retry-after-ms`中的标头在重试请求之前给出建议的等待时间（以毫秒为单位）。

如果应用程序定期遇到 HTTP 状态代码 429 响应，请考虑重新设计它以减少发出的请求数。 有关详细信息，请参阅[减少对应用配置的请求](./howto-best-practices.md#reduce-requests-made-to-app-configuration)

## <a name="my-application-receives-http-status-code-429-responses-why"></a>我的应用程序接收 HTTP 状态代码 429 响应。 为什么？

在这些情况下，您将收到 HTTP 状态代码 429 响应：

* 超过免费套餐中商店的每日请求限制。
* 临时限制，因为标准层中的存储请求率很高。
* 带宽使用过多。
* 尝试在超过存储报价时创建或修改密钥。

检查 429 响应的正文，了解请求失败的具体原因。

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>如何接收有关新版本的公告以及与应用配置相关的其他信息？

订阅我们的[GitHub 公告存储库](https://github.com/Azure/AppConfiguration-Announcements)。

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>如何报告问题或提出建议？

你可以直接在[GitHub](https://github.com/Azure/AppConfiguration/issues)上联系我们。

## <a name="next-steps"></a>后续步骤

* [关于 Azure 应用配置](./overview.md)
