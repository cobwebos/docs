---
title: 使用 Azure 应用配置将事件发送到 web 终结点
description: 了解如何使用 Azure 应用配置事件订阅将键值修改事件发送到 web 终结点
services: azure-app-configuration
author: lisaguthrie
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 93700af5e7fb3a4a1253424996ed04532c01f88c
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619594"
---
# <a name="route-azure-app-configuration-events-to-a-web-endpoint-with-azure-cli"></a>使用 Azure CLI 将 Azure 应用程序配置事件路由到 Web 终结点

本文介绍如何设置 Azure 应用配置事件订阅，以将键值修改事件发送到 web 终结点。 Azure 应用配置用户可以订阅在修改键值时发出的事件。 这些事件可触发 web 挂钩、Azure Functions、Azure 存储队列或 Azure 事件网格支持的任何其他事件处理程序。 通常，你会将事件发送到处理事件数据并执行操作的终结点。 但是，为了简化本文，你将事件发送到收集并显示消息的 Web 应用。

## <a name="prerequisites"></a>系统必备

- Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)。 可以选择使用 Azure Cloud Shell。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文需要运行最新版的 Azure CLI（2.0.24 或更高版本）。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

如果不使用 Cloud Shell，则必须先使用 `az login` 登录。

## <a name="create-a-resource-group"></a>创建资源组

事件网格主题是 Azure 资源，必须放置在 Azure 资源组中。 该资源组是在其中部署和管理 Azure 资源的逻辑集合。

使用“[az group create](/cli/azure/group)”命令创建资源组。 

以下示例在 *westus* 位置创建名为 `<resource_group_name>` 的资源组。  将 `<resource_group_name>` 替换为资源组的唯一名称。

```azurecli-interactive
az group create --name <resource_group_name> --location westus
```

## <a name="create-an-app-configuration-store"></a>创建应用配置存储区

将 `<appconfig_name>` 替换为配置存储的唯一名称，并使用之前创建的资源组 `<resource_group_name>`。 该名称必须唯一，因为它将用作 DNS 名称。

```azurecli-interactive
az appconfig create \
  --name <appconfig_name> \
  --location westus \
  --resource-group <resource_group_name> \
  --sku free
```

## <a name="create-a-message-endpoint"></a>创建消息终结点

在订阅主题之前，让我们创建事件消息的终结点。 通常情况下，终结点基于事件数据执行操作。 为了简化此快速入门，将部署用于显示事件消息的[预建的 Web 应用](https://github.com/Azure-Samples/azure-event-grid-viewer)。 所部署的解决方案包括应用服务计划、应用服务 Web 应用和 GitHub 中的源代码。

将 `<your-site-name>` 替换为 Web 应用的唯一名称。 Web 应用名称必须唯一，因为它是 DNS 条目的一部分。

```azurecli-interactive
$sitename=<your-site-name>

az group deployment create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

部署可能需要几分钟才能完成。 部署成功后，请查看 Web 应用以确保它正在运行。 在 Web 浏览器中导航到 `https://<your-site-name>.azurewebsites.net`

应会看到站点上当前未显示任何消息。

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store"></a>订阅你的应用配置存储

订阅主题，以告知事件网格要跟踪哪些事件以及要将这些事件发送到哪个位置。 以下示例订阅所创建的应用程序配置，并将 Web 应用中的 URL 作为事件通知的终结点传递。 将 `<event_subscription_name>` 替换为事件订阅的名称。 对于 `<resource_group_name>` 和 `<appconfig_name>`，请使用此前创建的值。

Web 应用的终结点必须包括后缀 `/api/updates/`。

```azurecli-interactive
appconfigId=$(az appconfig show --name <appconfig_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $appconfigId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

再次查看 Web 应用，并注意现已向该应用发送了订阅验证事件。 选择眼睛图标以展开事件数据。 事件网格发送验证事件，以便终结点可以验证它是否想要接收事件数据。 Web 应用包含用于验证订阅的代码。

![查看订阅事件](./media/quickstarts/event-grid/view-subscription-event.png)

## <a name="trigger-an-app-configuration-event"></a>触发应用程序配置事件

现在，让我们触发一个事件，看事件网格如何将消息分发到终结点。 使用前面指定的 `<appconfig_name>` 创建键-值。

```azurecli-interactive
az appconfig kv set --name <appconfig_name> --key Foo --value Bar --yes
```

现已触发事件，并且事件网格已将消息发送到订阅时配置的终结点。 查看 Web 应用以查看刚刚发送的事件。

```json
[{
  "id": "deb8e00d-8c64-4b6e-9cab-282259c7674f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/eventDemoGroup/providers/microsoft.appconfiguration/configurationstores/{appconfig-name}",
  "subject": "https://{appconfig-name}.azconfig.io/kv/Foo",
  "data": {
    "key": "Foo",
    "etag": "a1LIDdNEIV6wCnfv3xaip7fMXD3"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T18:59:54Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="clean-up-resources"></a>清理资源
如果你打算继续使用此应用程序配置和事件订阅，请不要清理在本文中创建的资源。 如果不打算继续学习，请使用以下命令删除本文中创建的资源。

将 `<resource_group_name>` 替换为上面创建的资源组。

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>后续步骤

了解如何创建主题和事件订阅后，请详细了解键-值事件以及事件网格的功能：

- [对键-值事件作出反应](concept-app-configuration-event.md)
- [关于事件网格](../event-grid/overview.md)
- [Azure 事件网格处理程序](../event-grid/event-handlers.md)
