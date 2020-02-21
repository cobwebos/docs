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
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523644"
---
# <a name="point-in-time-snapshot"></a>时间点快照

Azure 应用配置维护对键值对所做更改的记录。 此记录提供键值更改的时间线。 您可以重新构造任何键-值的历史记录，并在过去七天内的任何时刻提供其过去的值。 使用此功能，可以 "向后移动" 并检索旧的键值。 例如，你可以恢复最新部署之前使用的配置设置，以便将应用程序回滚到以前的配置。

## <a name="key-value-retrieval"></a>键值检索

您可以使用 Azure PowerShell 检索过去的键值。  使用 `az appconfig revision list`，添加适当的参数来检索所需的值。  通过提供存储名称（`--name {app-config-store-name}`）或使用连接字符串（`--connection-string {your-connection-string}`）来指定 Azure 应用配置实例。 通过指定特定的时间点（`--datetime`），并通过指定要返回的最大项数（`--top`）来限制输出。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

检索对键值的所有记录的更改。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

检索键 `environment` 的所有记录的更改和标签 `test` 和 `prod`。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

检索层次结构密钥空间 `environment:prod`中记录的所有更改。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

在特定时间点检索密钥 `color` 的所有记录的更改。

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

检索键值的最后10个记录的更改，并仅返回 `key`、`label`和 `last-modified` 时间戳的值。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建一个 ASP.NET Core Web 应用](./quickstart-aspnet-core-app.md)  
