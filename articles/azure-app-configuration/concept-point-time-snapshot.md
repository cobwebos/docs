---
title: 从时间点检索键值对
titleSuffix: Azure App Configuration
description: 在 Azure 应用配置中使用时间点快照检索旧的键值对
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: b706b5d5ec68daa10fd6eac237b7b7416764167b
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830098"
---
# <a name="point-in-time-snapshot"></a>时间点快照

Azure 应用配置维护对键值所做更改的记录。 此记录提供键值更改的时间线。 您可以重新构造任何键-值的历史记录，并在关键历史记录期间的任何时刻提供其过去的值 (7 天用于免费层商店，或在30天内提供) 的标准层商店。 使用此功能，可以 "向后移动" 并检索旧的键值。 例如，你可以恢复最新部署之前使用的配置设置，以便将应用程序回滚到以前的配置。

## <a name="key-value-retrieval"></a>键值检索

您可以使用 Azure 门户或 CLI 来检索过去的键值。 在 Azure CLI 中，使用 `az appconfig revision list` 添加适当的参数以检索所需的值。  通过提供商店名称 (`--name <app-config-store-name>`) 或使用 () 的连接字符串来指定 Azure 应用配置实例 `--connection-string <your-connection-string>` 。 通过指定 () 的特定时间点 `--datetime` ，并通过指定 () 返回的最大项数来限制输出 `--top` 。

如果尚未本地安装 Azure CLI，则可以选择使用 Azure Cloud Shell。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

检索对键值的所有记录的更改。

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name>.
```

检索密钥和标签的所有记录的更改 `environment` `test` `prod` 。

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

检索层次结构密钥空间中所有记录的更改 `environment:prod` 。

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

在特定时间点检索密钥的所有记录的更改 `color` 。

```azurecli-interactive
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

检索键值的最后10个记录的更改，并只返回 `key` 、 `label` 和时间戳的值 `last_modified` 。

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建一个 ASP.NET Core Web 应用](./quickstart-aspnet-core-app.md)  
