---
title: Azure 应用配置最佳做法 |Microsoft Docs
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
ms.openlocfilehash: 37f93099027f810e8089119536e089e07080d0bc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898635"
---
# <a name="azure-app-configuration-best-practices"></a>Azure 应用配置最佳实践

本文介绍使用 Azure 应用配置时的常见模式和最佳实践。

## <a name="key-groupings"></a>键分组

应用配置提供了两个用于组织密钥的选项：

* 密钥前缀
* 标签

可以使用一个或两个选项对密钥进行分组。

*键前缀*是键的开始部分。 您可以通过在名称中使用相同的前缀对一组密钥进行逻辑分组。 前缀可以包含由分隔符连接的多个组件，如 `/`（类似于 URL 路径），以形成一个命名空间。 当你在一个应用配置存储中存储多个应用程序、组件服务和环境的密钥时，此类层次结构非常有用。

需要记住的重要一点是，应用程序代码会引用这些密钥来检索相应设置的值。 密钥不应更改，否则每次发生时都必须修改你的代码。

*标签*是键上的一个属性。 它们用于创建密钥的变体。 例如，可以将标签分配给多个版本的密钥。 版本可以是迭代、环境或某些其他上下文信息。 您的应用程序可以通过指定其他标签来请求一组完全不同的键值。 因此，所有键引用在代码中保持不变。

## <a name="key-value-compositions"></a>键-值组合

应用配置将存储的所有密钥视为独立的实体。 应用配置不会尝试推断键之间的任何关系，也不会根据层次结构继承键值。 不过，你可以通过在应用程序代码中结合使用带有正确配置堆栈的标签来聚合多组密钥。

接下来举例说明。 假设你有一个名为**Asset1**的设置，其值可能因开发环境而异。 创建名为 "Asset1" 的项，其标签为空，标签为 "开发"。 在第一个标签中，你为**Asset1**设置了默认值，并在后者中为 "开发" 指定了一个特定值。

在代码中，首先检索没有任何标签的键值，然后使用 "开发" 标签第二次检索相同的一组键值。 当你第二次检索值时，将覆盖以前的键值。 .NET Core 配置系统允许您 "堆栈" 多组配置数据彼此之上。 如果一个键存在于多个集中，则使用包含它的最后一个集。 使用新式编程框架（如 .NET Core）时，如果使用本机配置提供程序来访问应用配置，则可免费获取此堆栈功能。 下面的代码片段演示如何在 .NET Core 应用程序中实现堆栈：

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>应用配置启动

若要访问应用配置存储，可以使用 Azure 门户中提供的连接字符串。 由于连接字符串包含凭据信息，因此它们被视为机密信息。 这些机密需要存储在 Azure Key Vault 中，你的代码必须通过身份验证才能 Key Vault 检索它们。

更好的选择是使用 Azure Active Directory 中的托管标识功能。 使用托管标识，只需使用 "应用配置终结点 URL" 即可启动对应用配置存储的访问。 可以在应用程序代码中嵌入 URL （例如，在*appsettings*文件中）。 有关详细信息，请参阅[与 Azure 托管标识集成](howto-integrate-azure-managed-service-identity.md)。

## <a name="app-or-function-access-to-app-configuration"></a>应用程序或函数对应用配置的访问

你可以使用以下任一方法为 web 应用或功能提供对应用配置的访问权限：

* 在 Azure 门户中，在应用服务的 "应用程序设置" 中输入应用配置存储的连接字符串。
* 将连接字符串存储到 Key Vault 中的应用配置存储，并[从应用服务引用它](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)。
* 使用 Azure 托管标识访问应用配置存储。 有关详细信息，请参阅[与 Azure 托管标识集成](howto-integrate-azure-managed-service-identity.md)。
* 将配置从应用配置推送到应用服务。 应用配置提供了将数据直接发送到应用服务的导出功能（Azure 门户和 Azure CLI）。 利用此方法，你无需更改应用程序代码。

## <a name="next-steps"></a>后续步骤

* [键和值](./concept-key-value.md)
