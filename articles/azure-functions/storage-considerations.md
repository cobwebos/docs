---
title: Azure Functions 的存储注意事项
description: 了解 Azure Functions 的存储要求以及如何加密存储的数据。
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 48ff2dedd997cccb76b13acdadc895504f656ea3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984157"
---
# <a name="storage-considerations-for-azure-functions"></a>Azure Functions 的存储注意事项

创建函数应用实例时，Azure Functions 需要一个 Azure 存储帐户。 函数应用可使用以下存储服务：


|存储服务  | Functions 的用途  |
|---------|---------|
| [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)     | 维护绑定状态和函数密钥。  <br/>另由 [Durable Functions 中的任务中心](durable/durable-functions-task-hubs.md)使用。 |
| [Azure 文件](../storage/files/storage-files-introduction.md)  | 用于存储和运行[消耗计划](functions-scale.md#consumption-plan)中的函数应用代码的文件共享。 |
| [Azure 队列存储](../storage/queues/storage-queues-introduction.md)     | 由 [Durable Functions 中的任务中心](durable/durable-functions-task-hubs.md)使用。   |
| [Azure 表存储](../storage/tables/table-storage-overview.md)  |  由 [Durable Functions 中的任务中心](durable/durable-functions-task-hubs.md)使用。       |

> [!IMPORTANT]
> 使用消耗托管计划时，函数代码和绑定配置文件存储在主存储帐户的 Azure 文件存储中。 删除主存储帐户时，此内容将随之删除且无法恢复。

## <a name="storage-account-requirements"></a>存储帐户要求

创建函数应用时，必须创建或链接到支持 Blob、队列和表存储的常规用途的 Azure 存储帐户。 这是因为，Functions 依赖于 Azure 存储执行管理触发器和记录函数执行等操作。 某些存储帐户不支持队列和表。 这些帐户包括仅限 Blob 的存储帐户、Azure 高级存储，以及启用了 ZRS 复制的常规用途存储帐户。 创建函数应用时，将从“存储帐户”边栏选项卡中筛选出这些不受支持的帐户。

若要了解有关存储帐户类型的详细信息，请参阅 [Azure 存储服务简介](../storage/common/storage-introduction.md#core-storage-services)。 

尽管可将现有的存储帐户用于函数应用，但必须确保该帐户满足这些要求。 可以保证在函数应用创建流中创建的存储帐户满足这些存储帐户要求。  

## <a name="storage-account-guidance"></a>有关存储帐户的指导

每个 Function App 都需要存储帐户才能运行。 如果该帐户已被删除，则函数应用不会运行。 若要解决存储相关的问题，请参阅[如何排查存储相关的问题](functions-recover-storage-account.md)。 以下附加注意事项适用于函数应用使用的存储帐户。

### <a name="storage-account-connection-setting"></a>存储帐户连接设置

存储帐户连接在 [AzureWebJobsStorage 应用程序设置](./functions-app-settings.md#azurewebjobsstorage)中维护。 

重新生成存储密钥时，必须更新存储帐户连接字符串。 [在此处阅读有关存储密钥管理的详细信息](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)。

### <a name="shared-storage-accounts"></a>共享存储帐户

多个函数应用可以共享同一存储帐户，不会导致任何问题。 例如，在 Visual Studio 中，可以使用 Azure 存储仿真器开发多个应用。 在这种情况下，仿真器的作用类似于单个存储帐户。 还可以使用函数应用所用的同一存储帐户来存储应用程序数据。 但在生产环境中，这种做法不一定恰当。

### <a name="optimize-storage-performance"></a>优化存储性能

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>存储数据加密

Azure 存储对静态存储帐户中的所有数据进行加密。 有关详细信息，请参阅[静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)。

默认情况下，使用 Microsoft 管理的密钥对数据进行加密。 为了更进一步控制加密密钥，可以提供客户管理的密钥，以用于对 Blob 和文件数据进行加密。 要使 Functions 能够访问存储帐户，这些密钥必须在 Azure Key Vault 中存在。 有关详细信息，请参阅[通过 Azure 门户使用 Azure Key Vault 配置客户管理的密钥](../storage/common/storage-encryption-keys-portal.md)。  

## <a name="mount-file-shares-linux"></a>装载文件共享（Linux）

可以将现有的 Azure 文件共享装载到 Linux function apps。 通过将共享装载到 Linux function app，你可以利用现有的机器学习模型或函数中的其他数据。 你可以使用[`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add)命令将现有共享装载到 Linux function app。 

在此命令中`share-name` ，是现有 Azure 文件共享的名称， `custom-id`可以是在装载到 function app 时唯一定义该共享的任何字符串。 此外， `mount-path`也是在函数应用中访问共享的路径。 `mount-path`必须采用格式`/dir-name`，且不能以`/home`开头。

有关完整示例，请参阅[创建 Python 函数应用和装载 Azure 文件共享](scripts/functions-cli-mount-files-storage-linux.md)中的脚本。 

目前仅支持`storage-type`的`AzureFiles` 。 只能将五个共享装载到给定的函数应用。 装载文件共享时，如果存储帐户在不同的区域中，则最多可增加300ms 的冷启动时间，甚至更多。

装载的共享可用于指定的`mount-path`函数代码。 例如，如果`mount-path`为`/path/to/mount`，则可以通过文件系统 api 访问目标目录，如下面的 Python 示例所示：

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>后续步骤

详细了解 Azure Functions 宿主选项。

> [!div class="nextstepaction"]
> [Azure Functions 的缩放和托管](functions-scale.md)


