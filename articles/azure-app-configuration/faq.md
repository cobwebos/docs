---
title: Azure 应用程序配置常见问题解答
description: 阅读常见问题解答 (常见问题的答案) 有关 Azure 应用配置，例如它与 Azure Key Vault 的不同之处。
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 10510774409a6b75687086290b32885b30d9131a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335375"
---
# <a name="azure-app-configuration-faq"></a>Azure 应用程序配置常见问题解答

本文解答有关 Azure 应用程序配置的常见问题。

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>“应用程序配置”与 Azure Key Vault 有什么不同？

应用程序配置可帮助开发人员管理应用程序设置和控制功能可用性。 它旨在简化处理复杂配置数据的许多任务。

应用程序配置支持：

- 分层命名空间
- 标记
- 广泛的查询
- 批量检索
- 专用管理操作
- 功能管理用户界面

应用程序配置对 Key Vault 进行补充，而它们应在大多数应用程序部署中并行使用。

## <a name="should-i-store-secrets-in-app-configuration"></a>我应该在应用程序配置中存储机密吗？

尽管应用程序配置提供了强化的安全性，但 Key Vault 仍然是存储应用程序机密的最佳位置。 Key Vault 提供硬件级加密、粒度访问策略和管理操作（如证书轮换）。

你可以创建引用 Key Vault 中存储的机密的应用程序配置值。 有关详细信息，请参阅[在 ASP.NET Core 应用中使用 Key Vault 引用](./use-key-vault-references-dotnet-core.md)。

## <a name="does-app-configuration-encrypt-my-data"></a>应用程序配置是否加密我的数据？

是。 应用程序配置加密它持有的所有键值，并加密网络通信。 键名称和标签充当检索配置数据的索引，且不进行加密。

## <a name="where-does-data-stored-in-app-configuration-reside"></a>应用配置中存储的数据驻留在何处？ 

应用配置中存储的客户数据位于创建客户应用配置存储的区域。 应用配置可能会将数据复制到 [配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) 以进行数据恢复，但不会复制或移动其地域外部的客户数据（如 [Azure 中的数据常驻服务](https://azure.microsoft.com/global-infrastructure/data-residency/)所定义）。 客户和最终用户可从全球任何位置移动、复制或访问客户数据。

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>应用程序配置与 Azure 应用服务有什么不同？

通过 Azure 应用服务，可以为每个应用服务实例定义应用设置。 这些设置作为环境变量传递给应用程序代码。 如果需要，可以将设置与特定部署槽关联。 有关详细信息，请参阅[配置应用设置](/azure/app-service/configure-common#configure-app-settings)。

而通过 Azure 应用程序配置，你能够定义可在多个应用之间共享的设置。 这包括在应用服务中运行的应用以及其他平台。 应用程序代码通过面向 .NET 和 Java 的配置提供程序、通过 Azure SDK 或直接通过 REST API 访问这些设置。

还可以在应用服务与应用程序配置之间导入和导出设置。 利用此功能，可以根据现有的应用服务设置快速设置新的应用程序配置存储区。 还可以与依赖于应用服务设置的现有应用共享配置。

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>应用程序配置中存储的键和值是否有任何大小限制？

单个键值项的限制为 10 KB。

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>我应该如何存储多个环境（测试、暂存、生产等）的配置？

你可以控制谁可以在每存储级别（按存储）访问应用程序配置。 对于需要不同权限的每个环境，请使用单独的存储区。 此方法可提供最佳的安全隔离。

如果不需要环境之间的安全隔离，可以使用标签来区分配置值。 [使用标签为不同环境启用不同配置](./howto-labels-aspnet-core.md)提供一个完整的示例。

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>建议的应用程序配置使用方法有哪些？

请参阅[最佳做法](./howto-best-practices.md)。

## <a name="how-much-does-app-configuration-cost"></a>应用程序配置的成本是多少？

目前有两种定价层：

- 免费层
- 标准层。

如果你在引入标准层之前创建了存储，则它会在功能正式可用后自动转入免费层。 可以选择升级到标准层，也可以继续使用免费层。

你不能将存储从标准层降级到免费层。 你可以在免费层中创建新存储，然后将配置数据导入该存储。

## <a name="which-app-configuration-tier-should-i-use"></a>我应该使用哪个应用程序配置层？

两个应用程序配置层都提供核心功能，包括配置设置、功能标志、Key Vault 引用、基本管理操作、指标和日志。

以下是有关选择层的注意事项。

- **每个订阅的资源数**：资源由单个配置存储区组成。 每个订阅在免费层中只能有一个配置存储区。 订阅在标准层中可以有无限数量的配置存储区。
- **每个资源的存储空间**：在免费层中，每个配置存储区限 10 MB 的存储空间。 在标准层中，每个配置存储区最多使用 1GB 的存储空间。
- **键历史记录**：“应用程序配置”会存储对键所做的所有更改的历史记录。 在免费层中，会存储七天的历史记录。 在标准层中，此历史记录存储 30 天。
- **每日请求数**：免费层存储限每日 1,000 个请求。 一旦存储量达到 1,000 个请求，它会针对后续所有请求返回 HTTP 状态代码 429，直到 UTC 午夜时间。

    对于标准层存储，每日前 200,000 个请求包含在每日费用中。 其他请求按超额计费。

- **服务级别协议**：标准层具有 99.9% 的 SLA 可用性。 免费层没有 SLA。
- **安全功能**：这两个层都包括基本安全功能，包括使用 Microsoft 管理的密钥进行加密、通过 HMAC 或 Azure Active Directory 进行身份验证、RBAC 支持和托管标识。 标准层提供更高级的安全功能，包括对客户管理的密钥的私有链接支持和加密。
- **成本**：标准层存储每日收取使用费。 对超过每日配额的请求也收取超额费用。 免费层的存储可免费使用。

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>我可以将存储从免费层升级到标准层吗？ 我可以将存储从标准层降级到免费层吗？

你可以随时从免费层升级到标准层。

你不能将存储从标准层降级到免费层。 你可以在免费层中创建新存储，然后[将配置数据导入该存储](howto-import-export-data.md)。

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>对应用程序配置发出的请求数有任何限制吗？

免费层中的配置存储区限每日 1,000 个请求。 当请求速率超过每小时 20,000 个请求时，标准层中的配置存储区可能会产生临时限制。

当存储达到其限制时，它将针对后续所有请求返回 HTTP 状态代码 429，直到过了时间周期。 响应中的 `retry-after-ms` 标头在重试请求之前会给出建议的等待时间（以毫秒为单位）。

如果应用程序经常遇到 HTTP 状态代码 429 响应，请考虑重新设计它以减少发出的请求数。 有关详细信息，请参阅[减少应用程序配置请求数](./howto-best-practices.md#reduce-requests-made-to-app-configuration)

## <a name="my-application-receives-http-status-code-429-responses-why"></a>我的应用程序接收到 HTTP 状态代码 429 响应。 为什么？

在以下情况下，会收到 HTTP 状态代码 429 响应：

* 超出了免费层中存储量的每日请求限额。
* 由于标准层中存储的请求速率过高，产生了临时限制。
* 过度使用带宽。
* 在超过存储配额时尝试创建或修改密钥。

检查 429 响应的正文，以了解请求失败的具体原因。

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>如何接收有关新版本的公告以及与应用程序配置相关的其他信息？

订阅我们的 [GitHub 公告存储库](https://github.com/Azure/AppConfiguration-Announcements)。

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>如何报告问题或提供建议？

可以直接在 [GitHub](https://github.com/Azure/AppConfiguration/issues) 上联系我们。

## <a name="next-steps"></a>后续步骤

* [关于 Azure 应用配置](./overview.md)
