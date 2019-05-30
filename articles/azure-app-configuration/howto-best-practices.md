---
title: Azure 应用程序配置最佳做法 |Microsoft Docs
description: 了解如何充分利用 Azure 应用程序配置
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 3d9a597e7ced631627a121f3f0757e472f9a4bae
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393589"
---
# <a name="azure-app-configuration-best-practices"></a>Azure 应用程序配置最佳做法

使用 Azure 应用配置时，本文将讨论常见的模式和最佳实践。

## <a name="key-groupings"></a>键分组

应用配置提供了用于组织键的两个选项：

* 键前缀
* 标签

可以使用一个或两个选项对键进行分组。

*键前缀*开头部分的键。 逻辑上可以通过在其名称中使用相同的前缀分组键的一组。 前缀可以包含多个组件，如连接通过分隔符、 `/`，类似于 URL 路径，以形成一个命名空间。 要将多个应用程序、 组件服务和环境的密钥存储在一个应用程序配置存储区时，此类层次结构非常有用。

要记住的重要一点是，密钥是在应用程序代码引用来检索相应的设置的值。 不应更改密钥，否则您将需要修改你的代码每次发生的。

*标签*是键的属性。 它们用于创建密钥的变体。 例如，可以将标签分配给多个版本的密钥。 版本可能是迭代、 一个环境或某些其他上下文信息。 通过指定另一个标签，你的应用程序可以请求一组完全不同的密钥值。 因此，所有键引用保持在代码中不变。

## <a name="key-value-compositions"></a>键-值组合

应用配置将作为独立的实体与它存储的所有键。 应用配置不会尝试推断任何键之间的关系或继承其层次结构所基于的密钥值。 您可以通过使用结合堆叠在应用程序代码中的正确配置的标签但是，聚合多个集的密钥。

接下来举例说明。 假设具有名为的设置**Asset1**，其值可能会有所不同根据开发环境。 创建名为一个空标签和一个名为"开发"标签"Asset1"的键。 在第一个标签，您放入的默认值为**Asset1**，并将特定的值为"开发"放在后一种。

在代码中，请首先检索不带任何标签的注册表项值，然后检索同一组键的值在第二个时间内使用"开发"标签。 时检索这些值的第二个时间，将覆盖以前的值的键。 .NET Core 配置系统可以"堆叠"多个彼此的配置数据集。 如果多个组中存在项，则使用包含它的最后一组。 现代编程框架，如.NET Core，即可获得此堆叠功能免费如果本机配置提供程序用于访问应用程序配置。 以下代码片段演示如何实现堆叠在.NET Core 应用程序：

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Use(KeyFilter.Any, LabelFilter.Null)
           .Use(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>应用配置 bootstrap

若要访问的应用配置存储区，可以使用其连接字符串，这是在 Azure 门户中可用。 连接字符串包含凭据的信息，因为它们是被视为机密。 这些机密需要在 Azure Key Vault 中存储和你的代码必须进行身份验证到密钥保管库来检索它们。

更好的选择是使用 Azure Active Directory 中的管理的标识功能。 使用托管标识，您可以到应用配置存储区需要仅将应用配置终结点 URL 启动访问。 可以在应用程序代码中嵌入 URL (例如，在*appsettings.json*文件)。 请参阅[与 Azure 集成托管标识](howto-integrate-azure-managed-service-identity.md)有关详细信息。

## <a name="app-or-function-access-to-app-configuration"></a>应用或函数应用配置权限

您可以使用任何以下方法访问提供 web 应用程序或函数应用配置：

* 通过 Azure 门户中，输入您的应用服务应用程序设置中的应用配置存储区的连接字符串。
* 将应用配置存储区的连接字符串存储在密钥保管库和[从应用服务中引用该](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)。
* 使用 Azure 托管的身份访问应用程序配置存储区。 有关详细信息，请参阅[与 Azure 集成托管标识](howto-integrate-azure-managed-service-identity.md)。
* 将配置从应用配置推送到应用服务。 应用配置提供了将数据发送到应用服务直接导出函数 （在 Azure 门户和 Azure CLI）。 使用此方法，不需要在所有更改应用程序代码。

## <a name="next-steps"></a>后续步骤

* [键和值](./concept-key-value.md)
