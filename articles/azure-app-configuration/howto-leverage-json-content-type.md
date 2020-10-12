---
title: 对键值使用 JSON 内容类型
titleSuffix: Azure App Configuration
description: 了解如何对键值使用 JSON 内容类型
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: how-to
ms.date: 08/03/2020
ms.author: avgupta
ms.openlocfilehash: 725beb50e55852e35ee4434539ff158f082059df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88121983"
---
# <a name="leverage-content-type-to-store-json-key-values-in-app-configuration"></a>利用内容类型在应用程序配置中存储 JSON 密钥值

数据在“应用程序配置”中存储为键值，默认情况下，值被视为字符串类型。 但是，可以通过利用与每个键值关联的内容类型属性来指定自定义类型，以便保留数据的原始类型，或让应用程序能够根据内容类型改变自己的行为。


## <a name="overview"></a>概述

在应用程序配置中，可将 JSON 媒体类型用作键值的内容类型，以获得如下好处：
- **更简单的数据管理**：在 Azure 门户中，管理键值（如数组）将变得更加容易。
- **增强的数据导出**：在数据导出过程中将保留基元类型、数组和 JSON 对象。
- **提供应用程序配置提供程序本机支持**：当应用程序中的“应用程序配置提供程序库”使用具有 JSON 内容类型的键值时，这些键值能够正常运行。

#### <a name="valid-json-content-type"></a>有效的 JSON 内容类型

[此处](https://www.iana.org/assignments/media-types/media-types.xhtml)定义的媒体类型可分配给与每个键值关联的内容类型。
媒体类型包括类型和子类型。 如果类型为 `application` 且子类型（或后缀）为 `json`，则媒体类型将被视为有效的 JSON 内容类型。
有效 JSON 内容类型的一些示例如下：

- application/json
- application/activity+json
- application/vnd.foobar+json;charset=utf-8

#### <a name="valid-json-values"></a>有效的 JSON 值

当键值具有 JSON 内容类型时，其值必须采用有效 JSON 格式，以便客户端正确处理它。 否则，客户端可能会失败或回退，并将其视为字符串格式。
有效 JSON 值的一些示例如下：

- "John Doe"
- 723
- false
- null
- "2020-01-01T12:34:56.789Z"
- [1, 2, 3, 4]
- {"ObjectSetting":{"Targeting":{"Default":true,"Level":"Information"}}}

> [!NOTE]
> 在本文的其余部分中，应用程序配置中具有有效 JSON 内容类型和有效 JSON 值的任何键值都将称为 JSON 键值。 

本教程介绍以下操作：
> [!div class="checklist"]
> * 在应用程序配置中创建 JSON 键值。
> * 从 JSON 文件中导入 JSON 键值。
> * 将 JSON 键值导出到 JSON 文件。
> * 在应用程序中使用 JSON 键值。


## <a name="prerequisites"></a>必备条件

- Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)。
- Azure CLI 的最新版本（2.10.0 或更高版本）。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 如果使用的是 Azure CLI，则必须先使用 `az login` 进行登录。 可以选择使用 Azure Cloud Shell。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-an-app-configuration-store"></a>创建应用配置存储区

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]


## <a name="create-json-key-values-in-app-configuration"></a>在应用程序配置中创建 JSON 键值

可以通过使用 Azure 门户、Azure CLI 或从 JSON 文件中导入来创建 JSON 键值。 本节中将介绍如何使用这三种方法创建相同的 JSON 键值。

### <a name="create-json-key-values-using-azure-portal"></a>使用 Azure 门户创建 JSON 键值

浏览到应用程序配置存储，选择“配置资源管理器” > “创建” > “键值”来添加以下键值对  ：

| 键 | 值 | 内容类型 |
|---|---|---|
| Settings:BackgroundColor | "Green" | application/json |
| Settings:FontSize | 24 | application/json |
| Settings:UseDefaultRouting | false | application/json |
| Settings:BlockedUsers | null | application/json |
| Settings:ReleaseDate | "2020-08-04T12:34:56.789Z" | application/json |
| Settings:RolloutPercentage | [25,50,75,100] | application/json |
| Settings:Logging | {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}} | application/json |

将“标签”留空，然后选择“应用” 。

### <a name="create-json-key-values-using-azure-cli"></a>使用 Azure CLI 创建 JSON 键值

以下命令将在应用程序配置存储中创建 JSON 键值。 将 `<appconfig_name>` 替换为应用程序配置存储的名称。

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

### <a name="import-json-key-values-from-a-file"></a>从文件导入 JSON 键值

创建一个具有以下内容的名为 `Import.json` 的 JSON 文件，并将其作为键值导入到应用程序配置中：

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
> `--depth` 参数用于将文件中的分层数据合并为键值对。 本教程指定了深度，目的是演示还可将 JSON 对象存储为应用程序配置中的值。 如果未指定深度，则默认情况下，JSON 对象将合并到最深级别。

在应用程序配置中，创建的 JSON 键值应如下所示：

![包含 JSON 键值的配置存储](./media/create-json-settings.png)


## <a name="export-json-key-values-to-a-file"></a>将 JSON 键值导出到文件

使用 JSON 键值的一个主要好处是在导出时能够保留值的原始数据类型。 如果应用程序配置中的键值不具有 JSON 内容类型，则其值将被视为字符串。 

请考虑以下没有 JSON 内容类型的键值：

| 键 | 值 | 内容类型 |
|---|---|---|
| Settings:FontSize | 24 | |
| Settings:UseDefaultRouting | false | |

将这些键值导出到 JSON 文件时，这些值将作为字符串导出：
```json
{
  "Settings": {
    "FontSize": "24",
    "UseDefaultRouting": "false"
  }
}
```

而将 JSON 键值导出到文件时，所有值都将保留其原始数据类型。 若要验证这一点，可将键值从应用程序配置导出到 JSON 文件。 你将看到导出的文件与先前导入的 `Import.json` 文件具有相同的内容。

```azurecli-interactive
az appconfig kv export -d file --format json --path "~/Export.json" --separator :
```

> [!NOTE]
> 如果你的应用程序配置存储具有没有 JSON 内容类型的某些键值，则这些值也将以字符串格式导出到相同的文件中。 如果只想导出 JSON 键值，请为 JSON 键值分配唯一的标签或前缀，并在导出过程中使用标签或前缀筛选。


## <a name="consuming-json-key-values-in-applications"></a>在应用程序中使用 JSON 键值

在应用程序中使用 JSON 键值的最简单方法是通过应用程序配置提供程序库来使用。 借助提供程序库，无需在应用程序中实现 JSON 键值的特殊处理。 对于应用程序，它们反序列化的方式始终与其他 JSON 配置提供程序库相同。 

> [!Important]
> .NET 配置提供程序版本 4.0.0（或更高版本）中提供了对 JSON 键值的本机支持。 有关更多详细信息，请参阅[后续步骤](#next-steps)。

如果使用 SDK 或 REST API 从应用程序配置中读取键值（基于内容类型），则由应用程序负责使用标准 JSON 反序列化程序对 JSON 键值的值进行反序列化。


## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在应用程序配置存储中使用 JSON 键值，请创建一个应用程序来使用以下键值：

* [ASP.NET Core 快速入门](./quickstart-aspnet-core-app.md)
    * 先决条件：[Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) 包 v4.0.0 or 更高版本。

* [.NET Core 快速入门](./quickstart-dotnet-core-app.md)
    * 先决条件：[Microsoft.Extensions.Configuration.AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration) 包 v4.0.0 or 更高版本。
