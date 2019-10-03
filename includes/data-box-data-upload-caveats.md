---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: b159ec8620fa8c93e4917f73be9b9898e1b4fbcc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244572"
---
- 不要直接将文件复制到任何预创建的共享。 需要在共享下创建文件夹，然后将文件复制到该文件夹。
- StorageAccount_BlockBlob 和 StorageAccount_PageBlob 下的文件夹为容器   。 例如，容器创建为 StorageAccount_BlockBlob/container 和 StorageAccount_PageBlob/container   。
- 直接在 StorageAccount_AzureFiles 下创建的每个文件夹都将转换为 Azure 文件共享  。
- 如果云中存在与正在复制的对象同名的现有 Azure 对象（例如 blob 或文件），则 Data Box 将覆盖云中的文件。
- 写入到 StorageAccount_BlockBlob 和 StorageAccount_PageBlob 共享中的每个文件将分别上传为块 blob 和页 blob   。
- Azure Blob 存储不支持目录。 如果在 StorageAccount_BlockBlob 文件夹下创建文件夹，将以 blob 的名义创建虚拟文件夹  。 对于 Azure 文件，将维护实际的目录结构。
- 在 StorageAccount_BlockBlob 和 StorageAccount_PageBlob 文件夹下创建的任何空目录层次结构（没有任何文件）都不会上传   。
- 如果将数据上传到 Azure 时发生任何错误，则会在目标存储帐户中创建一个错误日志。 当上传完成时，可以找到此错误日志的路径，并且可以查看此日志来采取纠正措施。 在验证上传的数据之前，不要删除源中的数据。
