---
title: Azure Batch 中的文件和目录
description: 从开发的角度来了解文件和目录及其在 Azure Batch 工作流中的运用。
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: e7babb7e2cfdbbe78f61be766c549c1e80cacf98
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83790864"
---
# <a name="files-and-directories-in-azure-batch"></a>Azure Batch 中的文件和目录

在 Azure Batch 中，每个任务都有一个工作目录，任务将在该目录中创建零个或多个文件和目录。 此工作目录可用于存储任务运行的程序、任务处理的数据，以及任务执行的处理的输出。 任务的所有文件和目录由任务用户拥有。

Batch 服务在节点上公开文件系统的一部分作为 *根目录*。 任务可通过引用 `AZ_BATCH_NODE_ROOT_DIR` 环境变量来访问根目录。 有关使用环境变量的详细信息，请参阅 [任务的环境设置](jobs-and-tasks.md#environment-settings-for-tasks)。

根目录包含以下目录结构：

![计算节点目录结构][media\files-and-directories\node-folder-structure.png]

- **applications**：包含计算节点上安装的应用程序包的详细信息。 任务可通过引用 `AZ_BATCH_APP_PACKAGE` 环境变量来访问此目录。

- **fsmounts**：该目录包含计算节点上装载的所有文件系统。 任务可通过引用 `AZ_BATCH_NODE_MOUNTS_DIR` 环境变量来访问此目录。 有关详细信息，请参阅 [在 Batch 池上装载虚拟文件系统](virtual-file-mount.md)。

- **共享**：此目录允许对节点上运行的“所有”任务进行读取/写入访问。 在节点上运行的任何任务都可以创建、读取、更新和删除此目录中的文件。 任务可通过引用 `AZ_BATCH_NODE_SHARED_DIR` 环境变量来访问此目录。

- **启动**：启动任务使用此目录作为它的工作目录。 由启动任务下载到的节点所有文件都存储在此处。 启动任务可以创建、读取、更新和删除此目录下的文件。 任务可通过引用 `AZ_BATCH_NODE_STARTUP_DIR` 环境变量来访问此目录。

- **volatile**：此目录供内部使用。 不保证此目录中的任何文件或此目录本身未来一定存在。

- **workitems**：此目录包含计算节点上作业及其任务的目录。

    在 workitems 目录中，为节点上运行的每个任务创建一个 Tasks 目录 。 此目录可通过引用 `AZ_BATCH_TASK_DIR` 环境变量进行访问。
    
    在每个 Tasks 目录中，Batch 服务会创建一个工作目录 (`wd`)，该目录的唯一路径由 `AZ_BATCH_TASK_WORKING_DIR` 环境变量进行指定。 此目录提供对任务的读/写访问权限。 任务可以创建、读取、更新和删除此目录下的文件。 此目录根据指定给任务的 *RetentionTime* 约束来保留。

    在任务执行期间，`stdout.txt` 和 `stderr.txt` 文件会写入 Tasks 文件夹。

> [!IMPORTANT]
> 从池中删除节点时，将一并删除节点上存储的所有文件。

## <a name="next-steps"></a>后续步骤

- 了解 Azure Batch 中的[错误处理和检测](error-handling.md)。