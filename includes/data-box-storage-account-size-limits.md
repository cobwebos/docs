---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 3e1f9c225a57e7d41f85c2a92dac989453057c51
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736971"
---
下面是对复制到存储帐户的数据的大小限制。 请确保上传的数据符合这些限制。 有关这些限制的最新信息，请参阅[Blob 存储的可伸缩性和性能目标](../articles/storage/blobs/scalability-targets.md)和[Azure 文件的可伸缩性和性能目标](../articles/storage/files/storage-files-scale-targets.md)。

| 复制到 Azure 存储帐户的数据的大小                      | 默认限制          |
|---------------------------------------------------------------------|------------------------|
| 块 Blob 和页 blob                                            | 2 PB，适用于美国和欧洲。<br>对于所有其他区域（包括英国），500 TB。  <br> 这包括来自所有源（包括 Data Box）的数据。|
| Azure 文件                                                          | 标准文件共享的最大大小 100TiB *、5 TB、高级文件共享100TiB 每个共享。<br> StorageAccount_AzureFiles 下的所有文件夹都须遵循此限制**。       |
