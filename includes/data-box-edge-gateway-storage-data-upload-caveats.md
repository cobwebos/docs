---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/14/2019
ms.author: alkohli
ms.openlocfilehash: 8bc4b78d262cf5f30076e90b40b92c4f3237924a
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333899"
---
将数据移动到 Azure 中时，下面的警告适用。

- 建议不要将多台设备写入到同一容器中。
- 如果云中存在与正在复制的对象同名的现有 Azure 对象（例如 blob 或文件），则设备将覆盖云中的文件。
- 在共享文件夹下创建的任何空目录层次结构（不含任何文件）都不会上传到 blob 容器。
- 对于大型文件，建议使用 robocopy，因为它会针对间歇性错误重试复制操作。
- 如果与 Azure 存储容器关联的共享上传的 blob 与创建时为该共享定义的 blob 类型不匹配，则不会更新此类 blob。 例如，在设备上创建块 Blob 共享。 将该共享与具有页 Blob 的现有云容器相关联。 刷新该共享以下载文件。 修改已作为页 Blob 存储在云中的某些已刷新文件。 你将看到上传失败。
