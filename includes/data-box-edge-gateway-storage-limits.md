---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/30/2019
ms.author: alkohli
ms.openlocfilehash: 51fde73565c9c4cdb21e998b8d43d303ceef062e
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561390"
---
本部分介绍 Azure 存储服务的限制，以及适用于 Azure Stack Edge/Data Box Gateway 服务的 Azure 文件、Azure 块 blob 和 Azure 页 blob 的必要命名约定。 请仔细查看存储限制并遵循所有建议。

有关 Azure 存储服务限制的最新信息以及对共享、容器和文件进行命名的最佳做法，请转到：

- [命名和引用容器](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [命名和引用共享](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [块 blob 和页 blob 约定](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> 如果存在超出 Azure 存储服务限制的任何文件或目录，或不符合 Azure 文件/Blob 命名约定，则不会通过 Azure Stack Edge/Data Box Gateway 服务将这些文件或目录引入到 Azure 存储中。