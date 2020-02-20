---
title: Azure 应用配置常见问题解答 |Microsoft Docs
description: 有关 Azure 应用配置的常见问题
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 4bcd8f042563fa381832fd629061a822f71e844a
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467583"
---
# <a name="azure-app-configuration-faq"></a>Azure 应用配置常见问题

本文解决了有关 Azure 应用配置的常见问题。

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>应用配置与 Azure Key Vault 有何不同之处？

应用配置适用于一组不同的用例：它可帮助开发人员管理应用程序设置和控制功能可用性。 它旨在简化处理复杂配置数据的许多任务。

应用配置支持：

- 分层命名空间
- 加
- 广泛的查询
- 批量检索
- 专用管理操作
- 功能管理用户界面

应用配置与 Key Vault 具有互补性，而这两个应用程序应在大多数应用程序部署中并行使用。

## <a name="should-i-store-secrets-in-app-configuration"></a>是否应在应用配置中存储机密？

尽管应用配置提供强化的安全性，但 Key Vault 仍是存储应用程序机密的最佳位置。 Key Vault 提供硬件级别的加密、精细的访问策略和管理操作，如证书轮换。

你可以创建引用存储在 Key Vault 中的密钥的应用配置值。 有关详细信息，请参阅[在 ASP.NET Core 应用中使用 Key Vault 引用](./use-key-vault-references-dotnet-core.md)。

## <a name="does-app-configuration-encrypt-my-data"></a>应用配置是否对数据进行加密？

是的。 应用配置对其持有的所有密钥值进行加密，并对网络通信进行加密。 键名称和标签用作检索配置数据和未加密的索引。

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>应用配置与 Azure App Service 设置有何不同？

Azure App Service 允许你为每个应用服务实例定义应用设置。 这些设置作为环境变量传递给应用程序代码。 如果需要，可以将设置与特定的部署槽相关联。 有关详细信息，请参阅[配置应用设置](/azure/app-service/configure-common#configure-app-settings)。

相反，Azure 应用配置允许您定义可在多个应用之间共享的设置。 这包括应用服务中运行的应用以及其他平台。 应用程序代码通过适用于 .NET 和 Java 的配置提供程序、通过 Azure SDK 或通过 REST Api 直接访问这些设置。

你还可以在应用服务和应用配置之间导入和导出设置。 此功能可让你基于现有应用服务设置快速设置新的应用配置存储。 你还可以将配置与依赖于应用服务设置的现有应用共享。

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>在应用配置中存储的密钥和值是否有任何大小限制？

单个键值项的限制为 10 KB。

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>如何为多个环境（测试、过渡、生产等）存储配置？

目前，你可以控制谁可以访问每个商店级别的应用配置。 对于需要不同权限的每个环境，请使用单独的存储区。 此方法可提供最佳的安全隔离。

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>使用应用配置的建议方法有哪些？

请参阅[最佳实践](./howto-best-practices.md)。

## <a name="how-much-does-app-configuration-cost"></a>应用配置的成本是多少？

有两个定价层：1）一个免费层和2个标准层。

如果在引入标准层之前创建了存储，则它会在公开上市时自动移至免费层。 你可以选择升级到标准层，或在免费层上保留（如果它满足你的需求）。

## <a name="which-app-configuration-tier-should-i-use"></a>我应该使用哪个应用配置层？

这两个应用配置层都提供核心功能，包括配置设置、功能标志、Key Vault 引用、基本管理操作、指标和日志。

下面是选择层的注意事项。

- **每个订阅的资源**：资源由单个配置存储组成。 每个订阅仅限免费层中的一个配置存储。 在标准层中，订阅可以有无限数量的配置存储。
- **每个资源的存储**：在免费级别，每个配置存储限制为 10 MB 的存储空间。 在标准层中，每个配置存储最多可使用 1 GB 的存储空间。
- **关键历史记录**：应用配置存储对密钥所做的所有更改的历史记录。 在免费层中，此历史记录存储七天。 在标准层中，此历史记录存储30天。
- **每日请求**数：免费层商店每天限制为1000请求。 在存储到达1000请求后，它将在午夜 UTC 之前返回所有请求的 HTTP 状态代码429。

    对于标准层存储，每天的前200000个请求都包含在每天的费用中。 其他请求按超额计费。

- **服务级别协议**：标准级别的 SLA 为99.9%。 免费层没有 SLA。
- **安全功能**：这两个层都包含基本的安全功能，包括对 Microsoft 托管密钥的加密、通过 HMAC 或 AZURE ACTIVE DIRECTORY、RBAC 支持和托管标识进行身份验证。 标准层提供更高级的安全功能，包括对客户管理的密钥的私有链接支持和加密。
- **成本**：标准层商店具有每日使用量。 对于超出每日分配的请求，还会收取超额费用。 使用免费层商店不会产生费用。

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>是否可以将商店从免费层升级到标准层？ 是否可将存储从标准层降级到免费层？

你可以随时从免费层升级到标准层。

不能将存储从标准层降级到免费层。 可以在免费层中创建新的存储，然后将[配置数据导入到该存储](howto-import-export-data.md)中。

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>如何收到有关新版本以及与应用配置相关的其他信息的公告？

订阅[GitHub 公告](https://github.com/Azure/AppConfiguration-Announcements)存储库。

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>如何报告问题或提出建议？

你可以直接在[GitHub](https://github.com/Azure/AppConfiguration/issues)上联系我们。

## <a name="next-steps"></a>后续步骤

* [关于 Azure 应用配置](./overview.md)
