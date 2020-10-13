---
title: 从 Azure 应用程序配置存储区中自动备份键值
description: 了解如何设置应用程序配置存储区之间的自动键值备份。
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-dotnet
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: a3c1699dd4b7b828c7dc652f14f431878f785061
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88207140"
---
# <a name="back-up-app-configuration-stores-automatically"></a>自动备份应用程序配置存储区

本文介绍如何设置从主 Azure 应用程序配置存储区到辅助存储区的自动键值备份。 自动备份使用 Azure 事件网格与应用程序配置的集成。 

设置自动备份后，对于配置存储区中键值的任何更改，应用程序配置会向 Azure 事件网格发布事件。 事件网格支持各种 Azure 服务，用户可以从这些服务中订阅在创建、更新或删除键值时发出的事件。

## <a name="overview"></a>概述

在本文中，你将使用 Azure 队列存储从事件网格接收事件，并使用 Azure Functions 的计时器触发器批量处理队列中的事件。 

当触发函数时，根据事件，函数将从主应用程序配置存储区获取已更改的键的最新值，并相应更新辅助存储。 此设置有助于在一个备份操作中结合在短时间内发生的多个更改，从而避免向应用程序配置存储区发出过多的请求。

![显示应用程序配置存储区备份体系结构的图表。](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>资源预配

备份应用程序配置存储区背后的动机是在不同 Azure 区域中使用多个配置存储区，以提高应用程序的异地复原能力。 为此，主存储和辅助存储应在不同的 Azure 区域中。 本教程中创建的所有其他资源可在你选择的任何区域进行预配。 这是因为，如果主要区域关闭，便没有需要备份的新数据，直到主要区域再次可以访问。

在本教程中，你将在 `centralus` 区域创建辅助存储，并在 `westus` 区域创建所有其他资源。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 [免费创建一个](https://azure.microsoft.com/free/)。 
- 包含 Azure 开发工作负载的 [Visual Studio 2019](https://visualstudio.microsoft.com/vs)。
- [.NET Core SDK](https://dotnet.microsoft.com/download)。
- Azure CLI 的最新版本（2.3.1 或更高版本）。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 如果使用 Azure CLI，需要先使用 `az login` 登录。 您可以选择使用 Azure Cloud Shell。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>创建资源组

该资源组是在其中部署和管理 Azure 资源的逻辑集合。

使用 [az group create](/cli/azure/group) 命令创建资源组。

以下示例在 `westus` 位置创建名为 `<resource_group_name>` 的资源组。  将 `<resource_group_name>` 替换为资源组的唯一名称。

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>创建应用程序配置存储区

在不同区域创建主应用程序配置和辅助应用程序配置存储区。
将  `<primary_appconfig_name>` 和 `<secondary_appconfig_name>` 替换为配置存储区的唯一名称。 每个存储名称必须唯一，因为它将用作 DNS 名称。

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-a-queue"></a>创建队列

创建存储帐户和队列，用于接收事件网格发布的事件。

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>订阅应用程序配置存储区事件

从主应用程序配置存储区订阅以下两个事件：

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

以下命令为发送到队列的两个事件创建事件网格订阅。 将终结点类型设置为 `storagequeue`，并将终结点设置为队列 ID。 将 `<event_subscription_name>` 替换为你为事件订阅选择的名称。

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-functions-for-handling-events-from-queue-storage"></a>创建用于从队列存储处理事件的函数

### <a name="set-up-with-ready-to-use-functions"></a>通过现成的函数进行设置

在本文中，你将使用具有以下属性的 C# 函数：
- 运行时堆栈 .NET Core 3.1
- Azure Functions 运行版本 3.x
- 计时器每 10 分钟触发一次的函数

为了让你能够轻松开始备份数据，我们已[测试并发布了一个函数](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup)，你无需对代码做出任何更改即可使用。 下载项目文件，并[将其从 Visual Studio 发布到自己的 Azure 函数应用](/azure/azure-functions/functions-develop-vs#publish-to-azure)。

> [!IMPORTANT]
> 不要对下载的代码中的环境变量进行任何更改。 你将在下一部分中创建所需的应用设置。
>

### <a name="build-your-own-function"></a>构建自己的函数

如果之前提供的示例代码不符合你的要求，你还可以创建自己的函数。 函数必须能够执行以下任务才能完成备份：
- 定期读取队列的内容，以查看其是否包含来自事件网格的任何通知。 有关实现的详细信息，请参阅[存储队列 SDK](/azure/storage/queues/storage-quickstart-queues-dotnet)。
- 如果队列包含[事件网格中的事件通知](/azure/azure-app-configuration/concept-app-configuration-event?branch=pr-en-us-112982#event-schema)，从事件信息中提取唯一的 `<key, label>` 信息。 键和标签的组合是主存储中“键值更改”的唯一标识符。
- 从主存储读取所有设置。 仅更新辅助存储中在队列中具有相应事件的设置。 从辅助存储中删除在队列中存在但主存储中不存在的所有设置。 可以使用[应用程序配置 SDK](https://github.com/Azure/AppConfiguration#sdks) 以编程方式访问配置存储区。
- 如果处理过程中没有异常，从队列中删除消息。
- 根据需要实现错误处理。 请参阅前面的代码示例，查看你可能想要处理的一些常见异常。

若要详细了解创建一个函数，请参阅：[在 Azure 中创建由计时器触发的函数](/azure/azure-functions/functions-create-scheduled-function)和[使用 Visual Studio 开发 Azure Functions](/azure/azure-functions/functions-develop-vs)。


> [!IMPORTANT]
> 使用最佳判断根据主配置存储区的更改频率选择计时器计划。 过于频繁地运行该函数可能会限制对存储的请求。
>


## <a name="create-function-app-settings"></a>创建函数应用设置

如果你使用的是我们提供的功能，则需要在函数应用中配置以下应用设置：
- `PrimaryStoreEndpoint`：主应用程序配置存储区的终结点。 例如 `https://{primary_appconfig_name}.azconfig.io`。
- `SecondaryStoreEndpoint`：辅助应用程序配置存储区的终结点。 例如 `https://{secondary_appconfig_name}.azconfig.io`。
- `StorageQueueUri`：队列 URI。 例如 `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`。

以下命令在函数应用中创建所需的应用设置。 将 `<function_app_name>` 替换为你的函数应用的名称。

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-the-function-app"></a>向函数应用的托管标识授予访问权限

使用以下命令或 [Azure 门户](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity)为函数应用添加系统分配的托管标识。

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> 要执行所需的资源创建和角色管理，帐户在相应的范围（订阅或资源组）需要 `Owner` 权限。 如果需有关要角色分配的帮助，请了解[如何使用 Azure 门户添加或删除 Azure 角色分配](/azure/role-based-access-control/role-assignments-portal)。

使用以下命令或 [Azure 门户](/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity#grant-access-to-app-configuration)向函数应用的托管标识授予对应用程序配置存储区的访问权限。 使用以下角色：
- 分配主应用程序配置存储区中的 `App Configuration Data Reader` 角色。
- 在辅助应用程序配置存储区分配 `App Configuration Data Owner` 角色。

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

使用以下命令或 [Azure 门户](/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal)向函数应用的托管标识授予对队列的访问权限。 在队列中分配 `Storage Queue Data Contributor` 角色。

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>触发应用程序配置事件

若要测试所有功能是否正常运行，可以从主存储创建、更新或删除键值。 在计时器触发 Azure Functions 几秒钟后，辅助存储中会自动显示此更改。

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

你已触发了该事件。 几分钟后，事件网格会将事件通知发送到你的队列。 在下次按计划运行函数后，查看辅助存储中的配置设置，以查看它是否包含主存储中更新的键值。

> [!NOTE]
> 可以在测试和故障排除期间[手动触发函数](/azure/azure-functions/functions-manually-run-non-http)，而无需等待计划的计数器触发。

确保备份函数成功运行后，可以看到辅助存储中现在有了该键。

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>故障排除

如果在辅助存储中看不到新设置：

- 在主存储中创建设置后，确保触发了备份函数。
- 事件网格可能无法及时将事件通知发送到队列。 查看队列是否仍包含来自主存储的事件通知。 如果是，请再次触发备份函数。
- 查看 [Azure Functions 日志](/azure/azure-functions/functions-create-scheduled-function#test-the-function)中是否记录了任何错误或警告。
- 使用 [Azure 门户](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#get-started-in-the-azure-portal)可确保 Azure 函数应用包含 Azure Functions 正在尝试读取的应用程序设置的正确值。
- 还可以使用 [Azure Application Insights](/azure/azure-functions/functions-monitoring?tabs=cmd) 为 Azure Functions 设置监视和警报。 


## <a name="clean-up-resources"></a>清理资源
如果你打算继续使用此应用程序配置和事件订阅，请不要清理在本文中创建的资源。 如果不打算继续学习，请使用以下命令删除本文中创建的资源。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>后续步骤

现在你已了解如何设置键值的自动备份，请详细了解如何提高应用程序的异地复原能力：

- [复原能力和灾难恢复](concept-disaster-recovery.md)
