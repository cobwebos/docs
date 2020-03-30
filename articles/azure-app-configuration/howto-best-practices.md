---
title: Azure 应用配置最佳实践 |微软文档
description: 了解如何最好地使用 Azure 应用配置
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
ms.openlocfilehash: df56f53b64a35737700529b80c004efeb31eaabc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348662"
---
# <a name="azure-app-configuration-best-practices"></a>Azure 应用配置最佳实践

本文讨论使用 Azure 应用配置时的常见模式和最佳做法。

## <a name="key-groupings"></a>关键分组

应用配置提供了两个用于组织密钥的选项：

* 键前缀
* 标签

您可以使用一个或两个选项对密钥进行分组。

*键前缀*是键的开头部分。 在一组键的名称中使用相同的前缀，可以逻辑地对一组键进行分组。 前缀可以包含由分隔符连接的多个组件，例如`/`，类似于 URL 路径，以形成命名空间。 当您在一个应用配置存储中存储许多应用程序、组件服务和环境的密钥时，此类层次结构非常有用。

需要记住的一个重要问题是，键是应用程序代码引用的，用于检索相应设置的值。 密钥不应更改，否则每次更改时都必须修改代码。

*标签*是键上的属性。 它们用于创建密钥的变体。 例如，您可以将标签分配给密钥的多个版本。 版本可以是迭代、环境或其他上下文信息。 应用程序可以通过指定另一个标签请求一组完全不同的键值。 因此，代码中的所有密钥引用都保持不变。

## <a name="key-value-compositions"></a>键值组合

应用配置将与其一起存储的所有密钥视为独立实体。 应用配置不会尝试推断键之间的任何关系或基于其层次结构继承键值。 但是，通过使用标签与应用程序代码中的正确配置堆叠相结合，可以聚合多组密钥。

接下来举例说明。 假设您有一个名为**Asset1**的设置，其值可能因开发环境而异。 创建名为"Asset1"的键，该键的标签为空标签，标签名为"开发"。 在第一个标签中，您将**Asset1**的默认值，并在后者中放置"开发"的特定值。

在代码中，首先检索没有任何标签的键值，然后使用"开发"标签第二次检索同一组键值。 第二次检索值时，将覆盖键的先前值。 .NET Core 配置系统允许您相互"堆叠"多组配置数据。 如果多个集中存在一个密钥，则使用包含该键的最后一个集。 使用现代编程框架（如 .NET Core），如果您使用本机配置提供程序访问应用配置，则可以免费获得此堆叠功能。 以下代码段显示了如何在 .NET Core 应用程序中实现堆叠：

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[使用标签为不同的环境启用不同的配置](./howto-labels-aspnet-core.md)提供了一个完整的示例。

## <a name="app-configuration-bootstrap"></a>应用配置引导

要访问应用配置存储，可以使用其连接字符串，该字符串在 Azure 门户中可用。 由于连接字符串包含凭据信息，因此它们被视为机密。 这些机密需要存储在 Azure 密钥保管库中，并且代码必须对密钥保管库进行身份验证才能检索它们。

更好的选项是在 Azure 活动目录中使用托管标识功能。 使用托管标识时，只需应用配置终结点 URL 来引导对应用配置存储的访问。 您可以将 URL 嵌入到应用程序代码中（例如，在*应用程序设置.json*文件中）。 有关详细信息[，请参阅与 Azure 托管标识集成](howto-integrate-azure-managed-service-identity.md)。

## <a name="app-or-function-access-to-app-configuration"></a>应用或功能对应用配置的访问

您可以使用以下任一方法为 Web 应用或功能提供对应用配置的访问：

* 通过 Azure 门户，在应用服务的应用程序设置中输入到应用配置存储的连接字符串。
* 将连接字符串存储在密钥保管库中的应用配置存储，并从[应用服务中引用它](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)。
* 使用 Azure 托管标识访问应用配置存储。 有关详细信息，请参阅与[Azure 托管标识集成](howto-integrate-azure-managed-service-identity.md)。
* 将配置从应用配置推送到应用服务。 应用配置提供一个导出功能（在 Azure 门户和 Azure CLI 中），该功能将数据直接发送到应用服务。 使用此方法，您根本不需要更改应用程序代码。

## <a name="reduce-requests-made-to-app-configuration"></a>减少对应用配置的请求

对应用配置的过多请求可能会导致限制或超额收费。 要减少请求数，

* 增加刷新超时，尤其是在配置值不频繁更改时。 使用[`SetCacheExpiration`方法](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration)指定新的刷新超时。

* 观看单个*哨点键*，而不是观看单个键。 仅当哨点键发生更改时，才刷新所有配置。 有关示例[，请参阅在ASP.NET核心应用中使用动态配置](enable-dynamic-configuration-aspnet-core.md)。

* 使用 Azure 事件网格在配置更改时接收通知，而不是经常轮询任何更改。 有关详细信息[，请参阅将 Azure 应用配置事件路由到 Web 终结点](./howto-app-configuration-event.md)

## <a name="importing-configuration-data-into-app-configuration"></a>将配置数据导入应用配置

应用配置提供了使用 Azure 门户或 CLI 从当前配置文件批量[导入](https://aka.ms/azconfig-importexport1)配置设置的选项。 您还可以使用相同的选项从应用配置导出值，例如在相关商店之间。 如果您想设置与 GitHub 存储库的持续同步，您可以使用我们的[GitHub 操作](https://aka.ms/azconfig-gha2)，以便您可以继续使用现有的源代码管理实践，同时获得应用配置的好处。

## <a name="next-steps"></a>后续步骤

* [键和值](./concept-key-value.md)
