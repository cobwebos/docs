---
title: 从时间点检索键值对
titleSuffix: Azure App Configuration
description: 使用 Azure 应用配置中的时点快照检索旧键值对
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523644"
---
# <a name="point-in-time-snapshot"></a>时间点快照

Azure 应用配置维护对键值对所做的更改的记录。 此记录提供键值更改的时间线。 您可以重建任何键值的历史记录，并在过去七天内随时提供其过去值。 使用此功能，您可以向后"时间旅行"并检索旧键值。 例如，您可以恢复最近部署之前使用的配置设置，以便将应用程序回滚到以前的配置。

## <a name="key-value-retrieval"></a>键值检索

可以使用 Azure PowerShell 检索过去的键值。  使用`az appconfig revision list`添加适当的参数来检索所需的值。  通过提供存储名称 （`--name {app-config-store-name}`） 或使用连接字符串 （）`--connection-string {your-connection-string}`指定 Azure 应用配置实例。 通过指定特定的时间点 （）`--datetime`和指定要返回的最大项数 （）`--top`来限制输出。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

检索对键值的所有记录更改。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

检索键`environment`和标签`test`的所有记录更改以及`prod`。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

检索分层键空间`environment:prod`中记录的所有更改。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

在特定时间点检索密钥`color`记录的所有更改。

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

检索对键值的最后 10 个记录的更改，并仅返回`key`的值`label`，`last-modified`和时间戳。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建一个 ASP.NET Core Web 应用](./quickstart-aspnet-core-app.md)  
