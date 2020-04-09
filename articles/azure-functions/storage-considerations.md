---
title: Azure 函数的存储注意事项
description: 了解 Azure 函数的存储要求和加密存储的数据。
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 48ff2dedd997cccb76b13acdadc895504f656ea3
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984157"
---
# <a name="storage-considerations-for-azure-functions"></a>Azure 函数的存储注意事项

创建函数应用实例时，Azure 函数需要 Azure 存储帐户。 您的功能应用可以使用以下存储服务：


|存储服务  | 函数使用  |
|---------|---------|
| [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)     | 维护绑定状态和功能键。  <br/>也由[持久函数中的任务中心](durable/durable-functions-task-hubs.md)使用。 |
| [Azure 文件](../storage/files/storage-files-introduction.md)  | 用于在[消耗计划中](functions-scale.md#consumption-plan)存储和运行函数应用代码的文件共享。 |
| [Azure 队列存储](../storage/queues/storage-queues-introduction.md)     | 由[持久函数中的任务中心](durable/durable-functions-task-hubs.md)使用。   |
| [Azure 表存储](../storage/tables/table-storage-overview.md)  |  由[持久函数中的任务中心](durable/durable-functions-task-hubs.md)使用。       |

> [!IMPORTANT]
> 使用消耗托管计划时，函数代码和绑定配置文件存储在主存储帐户的 Azure 文件存储中。 删除主存储帐户时，此内容将随之删除且无法恢复。

## <a name="storage-account-requirements"></a>存储帐户要求

创建函数应用时，必须创建或链接到支持 Blob、队列和表存储的常规用途的 Azure 存储帐户。 这是因为函数依赖于 Azure 存储来执行诸如管理触发器和日志记录函数执行等操作。 某些存储帐户不支持队列和表。 这些帐户包括仅 Blob 存储帐户、Azure 高级存储和具有 ZRS 复制的通用存储帐户。 创建函数应用时，这些不支持的帐户将从"存储帐户"边栏选项卡中筛选出来。

要了解有关存储帐户类型详细信息，请参阅[介绍 Azure 存储服务](../storage/common/storage-introduction.md#core-storage-services)。 

虽然您可以将现有存储帐户用于函数应用，但必须确保它满足这些要求。 作为功能应用创建流的一部分创建的存储帐户保证满足这些存储帐户要求。  

## <a name="storage-account-guidance"></a>存储帐户指南

每个 Function App 都需要存储帐户才能运行。 如果该帐户已删除，则函数应用将无法运行。 要排除与存储相关的问题，请参阅[如何解决与存储相关的问题](functions-recover-storage-account.md)。 以下其他注意事项适用于功能应用使用的存储帐户。

### <a name="storage-account-connection-setting"></a>存储帐户连接设置

存储帐户连接在 [AzureWebJobsStorage 应用程序设置](./functions-app-settings.md#azurewebjobsstorage)中维护。 

重新生成存储密钥时，必须更新存储帐户连接字符串。 [在此处阅读有关存储密钥管理的更多内容](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)。

### <a name="shared-storage-accounts"></a>共享存储帐户

多个功能应用可以共享同一个存储帐户，没有任何问题。 例如，在可视化工作室中，可以使用 Azure 存储仿真器开发多个应用。 在这种情况下，仿真程序的作用类似于单个存储帐户。 功能应用使用的相同存储帐户也可用于存储应用程序数据。 但是，这种方法在生产环境中并不总是一个好主意。

### <a name="optimize-storage-performance"></a>优化存储性能

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>存储数据加密

Azure 存储对静态存储帐户中的所有数据进行加密。 有关详细信息，请参阅[静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)。

默认情况下，使用 Microsoft 管理的密钥对数据进行加密。 为了更进一步控制加密密钥，可以提供客户管理的密钥，以用于对 Blob 和文件数据进行加密。 这些密钥必须存在于 Azure 密钥保管库中，以便函数能够访问存储帐户。 要了解更多信息，请参阅[使用 Azure 门户使用 Azure 密钥保管库配置客户管理密钥](../storage/common/storage-encryption-keys-portal.md)。  

## <a name="mount-file-shares-linux"></a>装载文件共享（Linux）

您可以将现有的 Azure 文件共享装载到 Linux 函数应用。 通过将共享安装到 Linux 函数应用，您可以利用函数中的现有机器学习模型或其他数据。 可以使用 命令[`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add)将现有共享装载到 Linux 函数应用。 

在此命令中，`share-name`是现有 Azure 文件共享的名称，`custom-id`可以是装载到函数应用时唯一定义共享的任何字符串。 此外，`mount-path`在函数应用中访问共享的路径。 `mount-path`必须采用格式`/dir-name`，并且不能从`/home`开始。

有关完整示例，请参阅创建 Python[函数应用中的脚本并装载 Azure 文件共享](scripts/functions-cli-mount-files-storage-linux.md)。 

目前，仅支持`storage-type``AzureFiles`的 一个。 您只能将五个共享装载到给定的函数应用。 安装文件共享可能会将冷启动时间至少增加 200-300 毫秒，或者当存储帐户位于其他区域时甚至更多。

装载的共享在指定的功能代码处`mount-path`可用。 例如，何时`mount-path`是`/path/to/mount`，可以通过文件系统 API 访问目标目录，如以下 Python 示例所示：

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>后续步骤

详细了解 Azure 函数托管选项。

> [!div class="nextstepaction"]
> [Azure Functions 的缩放和托管](functions-scale.md)


