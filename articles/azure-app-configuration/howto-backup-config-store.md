---
title: 自动备份 Azure 应用配置存储中的密钥值
description: 了解如何在应用配置存储之间设置密钥值的自动备份
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: 0b057172c822f893e602d60f77a285f0867cf368
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87367752"
---
# <a name="backup-app-configuration-stores-automatically"></a>自动存储备份应用配置

在本文中，你将了解如何设置从主应用配置存储到辅助存储的密钥值的自动备份。 它利用 Azure 事件网格与应用配置的集成。 设置后，应用配置会将事件发布到事件网格，以获取对配置存储区中的键值所做的任何更改。 事件网格支持各种 Azure 服务，用户可以使用这些服务订阅在创建、更新或删除键值时发出的事件。

## <a name="overview"></a>概述

在本教程中，你将使用 Azure 存储队列接收事件网格中的事件，并使用 Azure Functions 的计时器触发器来分批处理存储队列中的事件。 根据事件触发时，函数将从主应用配置存储中提取已更改的密钥的最新值，并相应地更新辅助存储区。 此设置有助于在一个备份操作中组合一小段时间内发生的多个更改，避免对应用配置存储发出过多的请求。

![应用配置存储备份体系结构](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>资源预配

备份应用配置存储的动机是在不同的 Azure 区域使用多个配置存储，以提高应用程序的异地复原能力。 若要实现此目的，主要和辅助存储应位于不同的 Azure 区域。 在本教程中创建的所有其他资源可在所选的任意区域中进行设置。 这是因为，如果主要区域关闭，则在主区域再次访问之前，不会进行任何新的备份。

在本教程中，你将在区域中创建辅助存储 `centralus` ，并在区域中创建所有其他资源 `westus` 。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)。 可以选择使用 Azure Cloud Shell。
- 适用于 Azure 开发工作负荷的[Visual Studio 2019](https://visualstudio.microsoft.com/vs) 。
- 下载并安装 [.NET Core SDK](https://dotnet.microsoft.com/download)。
- 最新版本的 Azure CLI （2.3.1 或更高版本）。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 如果使用 Azure CLI，则必须先使用登录 `az login` 。 可以选择使用 Azure Cloud Shell。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>创建资源组

该资源组是在其中部署和管理 Azure 资源的逻辑集合。

使用“[az group create](/cli/azure/group)”命令创建资源组。

以下示例在 `westus` 位置创建名为 `<resource_group_name>` 的资源组。  将 `<resource_group_name>` 替换为资源组的唯一名称。

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>创建应用配置存储

在不同的区域创建主要和辅助应用配置存储。
 `<primary_appconfig_name>`将和替换 `<secondary_appconfig_name>` 为配置存储区的唯一名称。 存储名称必须唯一，因为它用作 DNS 名称。

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

## <a name="create-azure-storage-queue"></a>创建 Azure 存储队列

创建存储帐户和存储队列，用于接收事件网格发布的事件。

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>订阅你的应用配置存储事件

从主应用配置存储中订阅以下两个事件：

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

以下命令为发送到存储队列的两个事件创建事件网格订阅，其中，终结点类型设置为 `storagequeue` ，并将终结点设置为队列 ID。 将替换为 `<event_subscription_name>` 您选择的事件订阅的名称。

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

## <a name="create-azure-functions-for-handling-events-from-storage-queue"></a>创建用于处理存储队列中事件的 Azure Functions

### <a name="setup-with-ready-to-use-azure-functions"></a>使用随时可用的 Azure Functions 设置

在本教程中，你将使用 c # Azure Functions，其中包含以下属性：
- 运行时堆栈 .NET Core 3。1
- Azure Functions 运行时版本3。x
- 每10分钟由计时器触发的函数

为了使你能够更轻松地开始备份数据，我们已经测试并发布了[Azure Functions](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) ，你可以在不对代码进行任何更改的情况下使用。 下载项目文件，并将[其从 Visual Studio 发布到你自己的 Azure Function App。](/azure/azure-functions/functions-develop-vs#publish-to-azure)

> [!IMPORTANT]
> 不要对已下载的代码中的环境变量进行任何更改。 你将在下一节中创建所需的应用设置。
>

### <a name="build-your-own-azure-functions"></a>构建您自己的 Azure Functions

如果上面提供的示例代码不能满足您的要求，您还可以创建自己的 Azure Functions。 函数必须能够执行下列任务才能完成备份：
- 定期读取存储队列中的内容，以查看它是否包含来自事件网格的任何通知。 有关实现的详细信息，请参阅[存储队列 SDK](/azure/storage/queues/storage-quickstart-queues-dotnet) 。
- 如果存储队列包含事件[网格中的事件通知](/azure/azure-app-configuration/concept-app-configuration-event?branch=pr-en-us-112982#event-schema)，请从事件消息中提取所有唯一的 <项、标签>。 键和标签组合是主存储区中键/值更改的唯一标识符。
- 从主存储中读取所有设置。 仅更新在存储队列中具有相应事件的辅助存储中的这些设置。 从存储队列中存在但在主存储中不存在的辅助存储中删除所有设置。 可以利用[应用配置 SDK](https://github.com/Azure/AppConfiguration#sdks)以编程方式访问配置存储。
- 如果在处理过程中没有异常，则从存储队列中删除消息。
- 请确保根据需要实现错误处理。 您可以参考上面的代码示例来查看一些可能需要处理的常见异常。

若要详细了解如何创建 Azure Functions，请参阅：[在 Azure 中创建由计时器触发的函数](/azure/azure-functions/functions-create-scheduled-function)，并[使用 Visual Studio 开发 Azure Functions](/azure/azure-functions/functions-develop-vs)。


> [!IMPORTANT]
> 使用最佳的毋庸置疑根据对主配置存储进行更改的频率，选择计时器计划。 请记住，运行函数过于频繁可能最终会限制你的应用程序的请求。
>


## <a name="create-azure-function-app-settings"></a>创建 Azure Function App 设置

如果你使用的是我们提供的 Azure Functions，则需要在 Azure Function App 中使用以下应用设置：
- `PrimaryStoreEndpoint`：主应用配置存储的终结点。 例如： `https://{primary_appconfig_name}.azconfig.io`
- `SecondaryStoreEndpoint`：辅助应用配置存储的终结点。 例如： `https://{secondary_appconfig_name}.azconfig.io`
- `StorageQueueUri`：存储队列 URI。 例如： `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`

以下命令在 Azure Function App 中创建所需的应用设置。 将替换 `<function_app_name>` 为 Azure Function App 的名称。

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-azure-function-app"></a>授权访问 Azure Function App 的托管标识

使用以下命令或[Azure 门户](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity)为 Azure Function App 添加系统分配的托管标识。

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> 若要执行所需的资源创建和角色管理，你 `'Owner'` 的帐户需要在适当的作用域（订阅或资源组）的权限。 如果需要有关角色分配的帮助，请了解[如何使用 Azure 门户添加或删除 Azure 角色分配](/azure/role-based-access-control/role-assignments-portal)。

使用以下命令或[Azure 门户](/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity#grant-access-to-app-configuration)向 Azure Function App 的托管标识授予对应用配置存储的访问权限。
- `App Configuration Data Reader`在主应用配置存储中分配角色。
- `App Configuration Data Owner`在辅助应用配置存储中分配角色。

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

使用以下命令或[Azure 门户](/azure/storage/common/storage-auth-aad-rbac-portal#assign-rbac-roles-using-the-azure-portal)授予 Azure Function App 的托管标识访问存储队列的权限。 
- `Storage Queue Data Contributor`在存储队列中分配角色。

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>触发应用程序配置事件

若要测试一切是否正常运行，可以从主存储创建/更新/删除密钥值。 计时器触发 Azure Functions 后，应在辅助存储中自动看到此更改。

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

你已经触发了该事件，稍后，事件网格会将事件通知发送到 Azure 存储队列。 在***下一次计划的 Azure Functions 运行后***，查看辅助存储中的配置设置，查看其是否包含主存储中的更新密钥值。

> [!NOTE]
> 你可以在测试和故障排除期间[手动触发你的 Azure Functions](/azure/azure-functions/functions-manually-run-non-http) ，而无需等待计划的计时器触发器。

确保备份功能成功运行后，可以看到该密钥现在存在于辅助存储中。

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

## <a name="troubleshooting"></a>疑难解答

如果未在辅助存储中看到新设置：

- 请确保在主存储中创建设置***后***触发了 backup 函数。
- 事件网格可能无法及时将事件通知发送到存储队列。 检查你的存储队列是否仍包含来自你的主存储的事件通知，如果是，则再次触发备份函数。
- 检查[Azure Functions 日志](/azure/azure-functions/functions-create-scheduled-function#test-the-function)中是否有任何错误或警告。
- 使用[Azure 门户](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#get-started-in-the-azure-portal)确保 Azure Function App 包含 Azure Functions 尝试读取的应用程序设置的正确值。
- 你还可以使用[Azure 应用程序 Insights](/azure/azure-functions/functions-monitoring?tabs=cmd)为你的 Azure Functions 设置监视和警报。 


## <a name="clean-up-resources"></a>清理资源
如果你打算继续使用此应用程序配置和事件订阅，请不要清理在本文中创建的资源。 如果不打算继续学习，请使用以下命令删除本文中创建的资源。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>后续步骤

了解如何设置密钥值的自动备份后，请详细了解如何提高应用程序的异地复原能力：

- [复原能力和灾难恢复](concept-disaster-recovery.md)
