---
title: "将 Azure Blob 存储事件路由到自定义 Web 终结点 | Microsoft Docs"
description: "使用 Azure 事件网格订阅 Blob 存储事件。"
services: storage,event-grid
keywords: 
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: 4f10d9b26cb75bee8103d986b7fa1197168c692f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-azure-cli"></a>使用 Azure CLI 将 Blob 存储事件路由到自定义 Web 终结点

Azure 事件网格是针对云的事件处理服务。 在本文中，请使用 Azure CLI 订阅 Blob 存储事件，然后触发可查看结果的事件。 

通常将事件发送到与该事件对应的终结点，例如 webhook 或 Azure Function。 为了简化本文中的示例，我们将事件发送到仅收集消息的 URL。 可从 [RequestBin](https://requestb.in/) 或 [Hookbin](https://hookbin.com/) 使用第三方工具创建此 URL。

> [!NOTE]
> RequestBin 和 Hookbin 并不适合在高吞吐量方案中使用。 在这里使用这些工具纯粹是为了演示。 如果一次推送多个事件，可能不会在工具中看到所有事件。

完成本文所述步骤后，即可看到事件数据已发送到某个终结点。

![事件数据](./media/storage-blob-event-quickstart/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文需要运行最新版的 Azure CLI（2.0.24 或更高版本）。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。

如果不使用云 Shell，则必须先使用 `az login` 登录。

## <a name="create-a-resource-group"></a>创建资源组

事件网格主题是 Azure 资源，必须放置在 Azure 资源组中。 该资源组是在其中部署和管理 Azure 资源的逻辑集合。

使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 

以下示例在 westcentralus 位置创建名为 `<resource_group_name>` 的资源组。  用资源组的唯一名称替换 `<resource_group_name>`。

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-storage-account"></a>创建存储帐户

要使用 Blob 存储事件，需要 [Blob 存储帐户](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts)或[常规用途 v2 存储帐户](../common/storage-account-options.md#general-purpose-v2)。 常规用途 v2 (GPv2) 是支持所有存储服务（包括 Blob、文件、队列和表）的所有功能的存储帐户。 Blob 存储帐户是将非结构化数据作为 Blob（对象）存储在 Azure 存储的专用存储帐户。 Blob 存储帐户类似于常规用途存储帐户，并且具有现在使用的所有卓越的耐用性、可用性、伸缩性和性能功能，包括用于块 blob 和追加 blob 的 100% API 一致性。 对于仅需要块 blob 或追加 blob 存储的应用程序，我们建议使用 Blob 存储帐户。 

> [!NOTE]
> 存储事件的可用性与事件网格[可用性](../../event-grid/overview.md)相关联，当事件网格在其他地区可用时，存储事件也同样可用。

将 `<storage_account_name>` 替换为存储帐户的唯一名称，将 `<resource_group_name>` 替换为此前创建的资源组。

```azurecli-interactive
az storage account create \
  --name <storage_account_name> \
  --location westcentralus \
  --resource-group <resource_group_name> \
  --sku Standard_LRS \
  --kind BlobStorage \
  --access-tier Hot
```

## <a name="create-a-message-endpoint"></a>创建消息终结点

在订阅主题之前, 让我们创建事件消息的终结点。 与其编写代码来响应事件，不如创建一个终结点来收集消息，方便你查看。 RequestBin 和 Hookbin 是第三方工具，用于创建终结点和查看发送到其中的请求。 转到 [RequestBin](https://requestb.in/)，然后单击“创建 RequestBin”；或者转到 [Hookbin](https://hookbin.com/)，然后单击“新建终结点”。  复制 bin URL，因为在订阅主题时需要它。

## <a name="subscribe-to-your-storage-account"></a>订阅存储帐户

订阅主题是为了告知事件网格要跟踪哪些事件。以下示例订阅你所创建的存储帐户，并将 RequestBin 或 Hookbin 中的 URL 作为事件通知的终结点传递。 将 `<event_subscription_name>` 替换为事件订阅的唯一名称，将 `<endpoint_URL>` 替换为上一部分的值。 在订阅时指定终结点，然后事件网格就会负责将事件路由到该终结点。 对于 `<resource_group_name>` 和 `<storage_account_name>`，请使用此前创建的值。  

```azurecli-interactive
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

## <a name="trigger-an-event-from-blob-storage"></a>触发 Blob 存储中的事件

现在，让我们触发一个事件，看事件网格如何将消息分发到终结点。 首先配置存储帐户的名称和密钥，然后创建容器，再创建并上传文件。 同样，对于 `<storage_account_name>` 和 `<resource_group_name>`，请使用此前创建的值。

```azurecli-interactive
export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY="$(az storage account keys list --account-name <storage_account_name> --resource-group <resource_group_name> --query "[0].value" --output tsv)"

az storage container create --name testcontainer

touch testfile.txt
az storage blob upload --file testfile.txt --container-name testcontainer --name testfile.txt
```

你已经触发事件，而事件网格则已将消息发送到你在订阅时配置的终结点。 浏览到此前创建的终结点 URL。 或者，在打开的浏览器中单击“刷新”。 此时会看到刚发送的事件。 

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "text/plain",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/testcontainer/testblob1.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>清理资源
如果打算继续使用此存储帐户和事件订阅，请勿清除本文中创建的资源。 如果不打算继续学习，请使用以下命令删除本文中创建的资源。

将 `<resource_group_name>` 替换为上面创建的资源组。

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>后续步骤

了解如何创建主题和事件订阅以后，即可进一步学习 Blob 存储事件以及事件网格的功能：

- [对 Blob 存储事件作出反应](storage-blob-event-overview.md)
- [关于事件网格](../../event-grid/overview.md)
