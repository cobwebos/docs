---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 5c5732a825116d4762c3a27bcbf9eac195327afb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85378414"
---
下面是对复制到存储帐户的数据的大小限制。 请确保上传的数据符合这些限制。 有关这些限制的最新信息，请参阅 [Blob 存储的可伸缩性和性能目标](../articles/storage/blobs/scalability-targets.md)和 [Azure 文件存储的可伸缩性和性能目标](../articles/storage/files/storage-files-scale-targets.md)。

| 复制到 Azure 存储帐户的数据的大小                      | 默认限制          |
|---------------------------------------------------------------------|------------------------|
| 块 Blob 和页 blob                                            | 最大限制与为[Azure 订阅定义的存储限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)相同，并且包括来自所有源的数据，包括 Data Box。|
| Azure 文件                                                          | 标准文件共享的最大大小为 5 TB <br> 高级文件共享的最大大小为每个共享100TiB。<br> StorageAccount_AzureFiles 下的所有文件夹都须遵循此限制  。       |
