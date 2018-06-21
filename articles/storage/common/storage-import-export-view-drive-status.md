---
title: 查看 Azure 导入/导出作业的状态 | Microsoft Docs
description: 了解如何查看导入/导出作业以及所用驱动器的的状态。
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: 176cbf190b6442682a222eb4f24b6583fb87a46b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659225"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>查看 Azure 导入/导出作业的状态

本文提供有关如何查看 Azure 导入/导出作业的驱动器和作业状态的信息。 Azure 导入/导出服务用于将大量数据安全地传输到 Azure Blob 和 Azure文件。 该服务还用于从 Azure Blob 存储导出数据。  

## <a name="view-job-and-drive-status"></a>查看作业和驱动器状态
可以从 Azure 门户跟踪导入或导出作业的状态。 单击“导入/导出”选项卡。随即页面上显示作业列表。

![查看作业状态](./media/storage-import-export-service/jobstate.png)


## <a name="view-job-status"></a>查看作业状态

会看到以下作业状态之一，具体取决于驱动器处于哪个处理阶段。

| 作业状态 | 说明 |
|:--- |:--- |
| Creating | 创建作业后，其状态设置为“Creating”。 当作业处于“Creating”状态时，导入/导出服务假设驱动器尚未寄送到数据中心。 作业可保持此状态最多两周，之后服务会自动将其删除。 |
| 寄送 | 寄出包裹后，应在 Azure 门户中更新跟踪信息。  这会将作业转换为“Shipping”状态。 作业会保持“Shipping”状态最多两周。 
| Received | 在数据中心收到所有驱动器后，作业状态设置为“Received”。 |
| 转移 | 至少一个驱动器已开始处理后，作业状态设置为“Transferring”。 有关详细信息，请转到[驱动器状态](#view-drive-status)。 |
| 打包 | 所有驱动器都完成处理完后，作业会进入“Packaging”状态，直到将驱动器寄回给你。 |
| 已完成 | 在将所有驱动器寄回给你后，如果完成作业时未出现错误，作业状态会设置为“Completed”。 作业会在保持“Completed”状态 90 天后自动删除。 |
| 已关闭 | 在将所有驱动器寄回给你后，如果处理作业期间出现任何错误，作业状态会设置为“Closed”。 作业会在保持“Closed”状态 90 天后自动删除。 |

## <a name="view-drive-status"></a>查看驱动器状态

下表描述了单个驱动器在导入或导出作业中转换时的生命周期。 可在 Azure 门户查看作业中每个驱动器的当前状态。

下表描述了每个驱动器在作业中可能经历的每种状态。

| 驱动器状态 | 说明 |
|:--- |:--- |
| Specified | 对于导入作业，在 Azure 门户中创建作业时，驱动器的初始状态为“Specified”。 对于导出作业，由于在创建作业时未指定驱动器，因此驱动器的初始状态为“Received”。 |
| Received | 导入/导出服务为导入作业处理从货运公司收到的驱动器后，驱动器将转换为“Received”状态。 对于导出作业，初始驱动器状态为“Received”。 |
| NeverReceived | 当作业的包裹已送达但包裹不包含驱动器时，驱动器将转换为“NeverReceived”状态。 如果在服务收到发货信息后的两周内包裹未送达数据中心，驱动器也会转换为此状态。 |
| 转移 | 服务开始将数据从驱动器传输到 Azure 存储时，驱动器将转换为“Transferring”状态。 |
| 已完成 | 服务成功传输所有数据且未出错时，驱动器将转换为“Completed”状态。
| CompletedMoreInfo | 如果服务在从驱动器中复制数据或将数据复制到驱动器时遇到一些问题，驱动器将转换为“CompletedMoreInfo”状态。 信息可以包含有关覆盖 Blob 的错误、警告或信息性消息。
| ShippedBack | 驱动器从数据中心寄送到回邮地址后，驱动器将转换为“ShippedBack”状态。 |

Azure 门户中的此映像会显示示例作业的驱动器状态：

![查看驱动器状态](./media/storage-import-export-service/drivestate.png)

下表描述了驱动器故障状态以及针对每种状态采取的措施。

| 驱动器状态 | 事件 | 解决方法/后续步骤 |
|:--- |:--- |:--- |
| NeverReceived | 标记为“NeverReceived”的驱动器（因为在作业寄送过程中未收到）通过另一次寄送送达。 | 运营团队将驱动器转换为“Received”状态。 |
| 不适用 | 不属于任何作业的驱动器将作为其他作业的一部分送至数据中心。 | 完成与原始包裹关联的作业后，驱动器将标记为额外驱动器并寄回给你。 |

## <a name="time-to-process-job"></a>处理作业的时间
处理导入/导出作业所需花费的时间取决于多个因素，例如：

-  寄送时间
-  数据中心的负载
-  作业类型和待复制数据的大小
-  作业中的磁盘数目。 

导入/导出服务没有 SLA，但在收到磁盘之后，服务力求在 7 到 10 天内完成复制。 除了在 Azure 门户上发布的状态以外，REST API 还可用于跟踪作业进度。 [列出作业]()操作 API 调用中的完成百分比参数提供复制进度的百分比。


## <a name="next-steps"></a>后续步骤

* [设置 WAImportExport 工具](storage-import-export-tool-how-to.md)
* [使用 AzCopy 命令行实用程序传输数据](storage-use-azcopy.md)
* [Azure 导入/导出 REST API 示例](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

