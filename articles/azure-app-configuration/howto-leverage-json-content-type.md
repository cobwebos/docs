---
title: 将 JSON content-type 值用于键值
titleSuffix: Azure App Configuration
description: 了解如何对键值使用 JSON content 类型
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: how-to
ms.date: 08/03/2020
ms.author: avgupta
ms.openlocfilehash: 725beb50e55852e35ee4434539ff158f082059df
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121983"
---
# <a name="leverage-content-type-to-store-json-key-values-in-app-configuration"></a>利用内容类型在应用配置中存储 JSON 密钥值

数据作为键值存储在应用配置中，在默认情况下，值被视为字符串类型。 但是，你可以通过利用与每个键值关联的 content-type 属性来指定自定义类型，以便你可以保留数据的原始类型，或让应用程序根据 content-type 的行为有所不同。


## <a name="overview"></a>概述

在应用配置中，你可以将 JSON 媒体类型用作关键值的内容类型，以获得以下好处：
- **更简单的数据管理**：在 Azure 门户中，管理键值（如数组）将变得更加容易。
- **增强的数据导出**：在数据导出过程中将保留基元类型、数组和 JSON 对象。
- **使用应用配置提供程序的本机支持**：在应用程序中，应用配置提供程序库使用具有 JSON 内容类型的键值。

#### <a name="valid-json-content-type"></a>有效的 JSON content-type 类型

[此处](https://www.iana.org/assignments/media-types/media-types.xhtml)定义的媒体类型可以分配给与每个键值相关联的 content-type。
媒体类型包括类型和子类型。 如果该类型为， `application` 并且子类型 (或后缀) 为，则将 `json` 该媒体类型视为有效的 JSON 内容类型。
有效的 JSON 内容类型的一些示例包括：

- application/json
- 应用程序/活动 + json
- application/vnd.apple.mpegurl. foobar + json; 字符集 = utf-8

#### <a name="valid-json-values"></a>有效的 JSON 值

当键-值具有 JSON content-type 时，其值必须为有效的 JSON 格式，以便客户端正确处理。 否则，客户端可能会失败或回退并将其视为字符串格式。
以下是一些有效 JSON 值的示例：

- "John Doe"
- 723
- false
- null
- "2020-01-01T12：34： 56.789 Z"
- [1, 2, 3, 4]
- {"ObjectSetting"： {"目标"： {"默认值： true，" Level "：" 信息 "}}}}

> [!NOTE]
> 对于本文的其余部分，应用配置中具有有效 JSON 内容类型和有效 JSON 值的任何键-值都称为**JSON 键-值**。 

在本教程中，你将学习如何执行以下操作：
> [!div class="checklist"]
> * 在应用配置中创建 JSON 密钥值。
> * 从 JSON 文件中导入 JSON 密钥值。
> * 将 JSON 密钥值导出到 JSON 文件。
> * 在应用程序中使用 JSON 密钥值。


## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)。
- 最新版本的 Azure CLI (2.10.0 或更高版本) 。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 如果使用 Azure CLI，则必须先使用登录 `az login` 。 可以选择使用 Azure Cloud Shell。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-an-app-configuration-store"></a>创建应用配置存储区

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]


## <a name="create-json-key-values-in-app-configuration"></a>在应用配置中创建 JSON 键-值

可以使用 Azure 门户、Azure CLI 或从 JSON 文件中导入来创建 JSON 键值。 在本部分中，你将了解如何使用所有三种方法创建相同的 JSON 键值。

### <a name="create-json-key-values-using-azure-portal"></a>使用 Azure 门户创建 JSON 键-值

浏览到应用配置存储，并选择 "**配置资源管理器**" "  >  **创建**  >  **键值**" 以添加以下键值对：

| 密钥 | 值 | 内容类型 |
|---|---|---|
| 设置： BackgroundColor | 钩 | application/json |
| 设置： FontSize | 24 | application/json |
| 设置： UseDefaultRouting | false | application/json |
| 设置： BlockedUsers | null | application/json |
| 设置： ReleaseDate | "2020-08-04T12：34： 56.789 Z" | application/json |
| 设置： RolloutPercentage | [25，50，75100] | application/json |
| 设置：日志记录 | {"Test"： {"Level"： "Debug"}，"生产"： {"Level"： "Warning"}} | application/json |

保留**标签**为空，并选择 "**应用**"。

### <a name="create-json-key-values-using-azure-cli"></a>使用 Azure CLI 创建 JSON 键-值

以下命令将在你的应用配置存储中创建 JSON 密钥值。 将替换 `<appconfig_name>` 为你的应用配置存储的名称。

```azurecli-interactive
appConfigName=<appconfig_name>
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BackgroundColor --value \"Green\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:FontSize --value 24
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:UseDefaultRouting --value false
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BlockedUsers --value null
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:ReleaseDate --value \"2020-08-04T12:34:56.789Z\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:RolloutPercentage --value [25,50,75,100]
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:Logging --value {\"Test\":{\"Level\":\"Debug\"},\"Prod\":{\"Level\":\"Warning\"}}
```

> [!IMPORTANT]
> 如果使用 Azure CLI 或 Azure Cloud Shell 来创建 JSON 键-值，则提供的值必须是一个转义的 JSON 字符串。

### <a name="import-json-key-values-from-a-file"></a>从文件中导入 JSON 密钥-值

使用以下内容创建一个名为的 JSON 文件 `Import.json` ，并将其作为键值导入到应用配置中：

```json
{
  "Settings": {
    "BackgroundColor": "Green",
    "BlockedUsers": null,
    "FontSize": 24,
    "Logging": {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}},
    "ReleaseDate": "2020-08-04T12:34:56.789Z",
    "RolloutPercentage": [25,50,75,100],
    "UseDefaultRouting": false
  }
}
```

```azurecli-interactive
az appconfig kv import -s file --format json --path "~/Import.json" --content-type "application/json" --separator : --depth 2
```

> [!Note]
> `--depth`自变量用于将文件中的分层数据平展为键值对。 在本教程中，为演示指定了深度，还可以将 JSON 对象存储为应用配置中的值。 如果未指定 depth，则默认情况下，JSON 对象将平展到最深级别。

在应用配置中，你创建的 JSON 密钥值应如下所示：

![包含 JSON 密钥值的配置存储](./media/create-json-settings.png)


## <a name="export-json-key-values-to-a-file"></a>将 JSON 密钥值导出到文件

使用 JSON 键值的主要好处之一是能够在导出时保留值的原始数据类型。 如果应用配置中的键值没有 JSON content-type 类型，则其值将被视为字符串。 

请考虑以下没有 JSON 内容类型的键值：

| 密钥 | 值 | 内容类型 |
|---|---|---|
| 设置： FontSize | 24 | |
| 设置： UseDefaultRouting | false | |

将这些键值导出到 JSON 文件时，值将作为字符串导出：
```json
{
  "Settings": {
    "FontSize": "24",
    "UseDefaultRouting": "false"
  }
}
```

但是，当你将 JSON 键值导出到文件时，所有值都将保留其原始数据类型。 若要验证这一点，请将应用程序配置中的键值导出到 JSON 文件。 您将看到导出的文件与 `Import.json` 您之前导入的文件具有相同的内容。

```azurecli-interactive
az appconfig kv export -d file --format json --path "~/Export.json" --separator :
```

> [!NOTE]
> 如果你的应用配置存储具有没有 JSON 内容类型的某些键值，则这些值也将导出到字符串格式的同一文件。 如果只想导出 JSON 键值，请为 JSON 键值指定唯一的标签或前缀，并在导出过程中使用标签或前缀筛选。


## <a name="consuming-json-key-values-in-applications"></a>在应用程序中使用 JSON 键值

在应用程序中使用 JSON 密钥值的最简单方法是通过应用配置提供程序库。 在提供程序库中，无需在应用程序中实现 JSON 键值的特殊处理。 对于您的应用程序，它们始终是以其他 JSON 配置提供程序库的相同方式进行反序列化的。 

> [!Important]
> .NET 配置提供程序版本4.0.0 中提供了对 JSON 键值的本机支持 (或更高版本的) 。 有关更多详细信息，请参阅[*后续步骤*](#next-steps)部分。

如果你使用 SDK 或 REST API 从应用配置读取键值（基于 content 类型），则应用程序将负责使用任何标准 JSON 反序列化程序对 JSON 密钥值的值进行反序列化。


## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在应用配置存储中使用 JSON 密钥值，请创建一个应用程序来使用以下键值：

* [ASP.NET Core 快速入门](./quickstart-aspnet-core-app.md)
    * 必备组件： [AppConfiguration. AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore)包 v 4.0.0 或更高版本。

* [.NET Core 快速入门](./quickstart-dotnet-core-app.md)
    * 先决条件： [Microsoft.Extensions.Configu。AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration) package v 4.0.0 或更高版本。
