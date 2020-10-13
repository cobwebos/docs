---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: 80411ac191d0385ddafd4a3cc490d322ce039689
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91643369"
---
- 不要直接将文件复制到任何预创建的共享。 需要在共享下创建文件夹，然后将文件复制到该文件夹。
- StorageAccount_BlockBlob 和 StorageAccount_PageBlob 下的文件夹为容器   。 例如，容器创建为 StorageAccount_BlockBlob/container 和 StorageAccount_PageBlob/container   。
- 直接在 StorageAccount_AzureFiles 下创建的每个文件夹都将转换为 Azure 文件共享  。
- 如果正在复制的对象与 Azure 对象（如 blob 或文件）具有相同的名称（例如 blob 或文件），则 Data Box 将覆盖云中的文件。
- 写入到 StorageAccount_BlockBlob 和 StorageAccount_PageBlob 共享中的每个文件将分别上传为块 blob 和页 blob   。
- Azure Blob 存储不支持目录。 如果在 StorageAccount_BlockBlob 文件夹下创建文件夹，将以 blob 的名义创建虚拟文件夹  。 对于 Azure 文件，将维护实际的目录结构。
- 在 StorageAccount_BlockBlob 和 StorageAccount_PageBlob 文件夹下创建的任何空目录层次结构（没有任何文件）都不会上传   。
- 如果将数据上传到 Azure 时发生任何错误，则会在目标存储帐户中创建一个错误日志。 上传完成后，此错误日志的路径可用，你可以查看日志以采取纠正措施。 在验证上传的数据之前，不要删除源中的数据。
- 使用在 [Azure Data Box 中保留文件 acl、属性和时间戳](../articles/databox/data-box-file-acls-preservation.md)来将数据上载到 Azure 文件时，可以保留文件元数据和 NTFS 权限。