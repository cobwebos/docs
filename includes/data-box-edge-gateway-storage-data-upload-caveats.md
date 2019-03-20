---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2019
ms.locfileid: "56659893"
---
将数据移动到 Azure 中时，下面的警告适用。

- 建议不要将多台设备写入到同一容器中。
- 如果云中存在与正在复制的对象同名的现有 Azure 对象（例如 blob 或文件），则设备将覆盖云中的文件。
- 在共享文件夹下创建的任何空目录层次结构（不含任何文件）都不会上传到 blob 容器。
- 可以使用拖放复制数据，并删除与文件资源管理器或通过命令行。 如果要复制的文件的总大小大于 10 GB，建议使用 Robocopy 或 rsync 等大容量复制程序。 大容量复制工具重试复制操作的间歇性错误，并提供额外的复原能力。
- 如果与 Azure 存储容器关联的共享上传的 blob 与创建时为该共享定义的 blob 类型不匹配，则不会更新此类 blob。 例如，在设备上创建块 Blob 共享。 将该共享与具有页 Blob 的现有云容器相关联。 刷新该共享以下载文件。 修改已作为页 Blob 存储在云中的某些已刷新文件。 你将看到上传失败。
