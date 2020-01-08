---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468130"
---
下面是对复制到存储帐户的数据的大小限制。 请确保上传的数据符合这些限制。 有关这些限制的最新信息，请参阅[Blob 存储的可伸缩性和性能目标](../articles/storage/blobs/scalability-targets.md)和[Azure 文件的可伸缩性和性能目标](../articles/storage/files/storage-files-scale-targets.md)。

| 复制到 Azure 存储帐户的数据的大小                      | 默认限制          |
|---------------------------------------------------------------------|------------------------|
| 块 Blob 和页 blob                                            | 2 PB，适用于美国和欧洲。<br>对于所有其他区域（包括英国），500 TB。  <br> 这包括来自所有源（包括 Data Box）的数据。|
| Azure 文件                                                          | 每个共享 5 TB。<br> StorageAccount_AzureFiles 下的所有文件夹都须遵循此限制。       |
