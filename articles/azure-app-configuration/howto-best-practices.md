---
title: Azure 应用程序配置最佳做法 | Microsoft Docs
description: 了解使用 Azure 应用配置时的最佳做法。 涉及的主题包括密钥分组、键值组合、应用配置启动，等等。
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
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: c45d1668ad39e9584a89921f46218ba243978a05
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078045"
---
# <a name="azure-app-configuration-best-practices"></a>Azure 应用程序配置最佳做法

本文介绍了使用 Azure 应用程序配置时的常见模式和最佳做法。

## <a name="key-groupings"></a>键分组

应用程序配置提供了两个用于组织键的选项：

* 键前缀
* 标签

可以使用一个或两个选项对键进行分组。

键前缀是键的开头部分。 可以通过在键名称中使用相同的前缀对一组键进行逻辑分组。 前缀可以包含由分隔符连接的多个构成部分，如 `/`（类似于 URL 路径），以形成一个命名空间。 当你在一个应用程序配置存储中存储多个应用程序、组件服务和环境的键时，此类层次结构非常有用。

需要记住的重要一点是，键是你的应用程序代码所引用的内容，用于检索相应设置的值。 键不应进行更改，否则每次发生更改时都必须修改你的代码。

标签是键上的一个属性。 它们用于创建键的变体。 例如，可以将标签分配给多个版本的键。 版本可以是迭代、环境或某些其他上下文信息。 你的应用程序可以通过指定其他标签来请求一组完全不同的键值。 其结果是，所有关键引用在代码中均保持不变。

## <a name="key-value-compositions"></a>键-值组合

应用程序配置将使用它存储的所有键视为独立的实体。 应用程序配置不会尝试推断键之间的任何关系，也不会基于键的层次结构来继承键值。 但是，可通过在应用程序代码中将标签和适当的配置堆叠配合使用来聚合多组键。

接下来举例说明。 假设你有一个名为“Asset1”的设置，其值可能因开发环境而异。 创建一个名为“Asset1”的键，其中包含一个空标签和一个名为“Development”的标签。 在第一个标签中，为“Asset1”输入默认值，在后者中为“Development”输入特定值。

在代码中，首先检索没有任何标签的键值，然后使用“Development”标签第二次检索相同的一组键值。 第二次检索该值时，将覆盖先前的键值。 .NET Core 配置系统使你可以“堆叠”多组配置数据。 如果一个键存在于多个集中，则使用包含该键的最后一个集。 通过新式编程框架（如 .NET Core），如果使用本机配置提供程序访问应用程序配置，则可以免费获得此堆叠功能。 以下代码片段演示如何在 .NET Core 应用程序中实现堆叠：

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[使用标签为不同环境启用不同配置](./howto-labels-aspnet-core.md)提供一个完整的示例。

## <a name="app-configuration-bootstrap"></a>应用程序配置启动

若要访问应用程序配置存储，可以使用其连接字符串，该字符串可在 Azure 门户中获取。 由于连接字符串包含凭据信息，因此它们被视为机密。 这些机密需要存储在 Azure Key Vault 中，你的代码必须向 Key Vault 进行身份验证才能检索这些机密。

更好的选择是使用 Azure Active Directory 中的托管标识功能。 通过托管标识，只需使用应用程序配置终结点 URL 即可启动对应用程序配置存储的访问。 可以将 URL 嵌入到应用程序代码中（例如嵌入到 appsettings.json 文件中）。 有关详细信息，请参阅[与 Azure 托管标识集成](howto-integrate-azure-managed-service-identity.md)。

## <a name="app-or-function-access-to-app-configuration"></a>应用或函数对应用程序配置的访问权限

你可以使用以下任一方法为 Web 应用或函数提供对应用程序配置的访问权限：

* 通过 Azure 门户，在应用服务的应用程序设置中输入应用程序配置存储的连接字符串。
* 将连接字符串存储到 Key Vault 中的应用程序配置存储，并[从应用服务中引用该字符串](../app-service/app-service-key-vault-references.md)。
* 使用 Azure 托管标识访问应用程序配置存储。 有关详细信息，请参阅[与 Azure 托管标识集成](howto-integrate-azure-managed-service-identity.md)。
* 将配置从应用程序配置推送到应用服务。 应用程序配置提供了可将数据直接发送到应用服务中的导出功能（在 Azure 门户和 Azure CLI 中）。 通过此方法，你无需更改应用程序代码。

## <a name="reduce-requests-made-to-app-configuration"></a>减少对应用程序配置发出的请求

对应用程序配置的请求过多可能会导致限制或超额费用。 若要减少发出的请求数，方法如下：

* 提高刷新超时，尤其是在配置值不经常更改的情况下。 使用 [`SetCacheExpiration` 方法](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration)指定新的刷新超时。

* 集中监视一个 sentinel 键，而不是监视独立的多个键。 仅当 sentinel 键更改时才刷新所有配置。 有关示例，请参阅[在 ASP.NET Core 应用中使用动态配置](enable-dynamic-configuration-aspnet-core.md)。

* 使用 Azure 事件网格在配置更改时接收通知，而不是通过不断轮询来发现更改。 有关详细信息，请参阅[将 Azure 应用程序配置事件路由到 Web 终结点](./howto-app-configuration-event.md)

## <a name="importing-configuration-data-into-app-configuration"></a>将配置数据导入到应用程序配置中

应用程序配置提供了使用 Azure 门户或 CLI 从当前配置文件批量[导入](./howto-import-export-data.md)配置设置的选项。 还可以使用相同的选项从应用程序配置中导出值，例如在相关存储之间导出值。 若要设置与 GitHub 存储库的持续同步，可以使用 [GitHub 操作](./concept-github-action.md)，这样可以继续使用现有的源代码管理实践，同时又能获得应用程序配置的优势。

## <a name="multi-region-deployment-in-app-configuration"></a>应用程序配置中的多区域部署

应用程序配置是一种区域服务。 对于在每个区域设有不同配置的应用程序，将这些配置存储在一个实例中可能会导致单一故障点。 跨多个区域为每个区域部署一个应用程序配置实例可能是一个更好的选择。 它可帮助实现区域性灾难恢复、提高性能以及实现安全孤岛。 按区域配置还可以减少延迟并使用单独的限制配额，因为限制是按实例执行的。 若要应用灾难恢复缓解，可以使用[多个配置存储](./concept-disaster-recovery.md)。 

## <a name="next-steps"></a>后续步骤

* [键和值](./concept-key-value.md)