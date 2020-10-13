---
title: 管理帐户
description: 了解 Azure Batch 帐户的构成
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ef87fab099c6ff7402b351664ee15a198d69a4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83722960"
---
# <a name="manage-your-batch-account"></a>管理 Batch 帐户

批处理帐户是批处理服务中唯一标识的实体。 所有处理都与一个 Batch 帐户相关联。

可以通过 [Azure 门户](batch-account-create-portal.md)或编程方式（例如使用[批处理管理 .NET 库](batch-management-dotnet.md)）创建 Azure Batch 帐户。 创建该帐户时，可以关联一个 Azure 存储帐户，用于存储与作业相关的输入和输出数据或应用程序。

可以在单个批处理帐户中运行多个批处理工作负荷，或者在相同订阅的不同 Azure 区域的批处理帐户之间分散工作负荷。

## <a name="components-of-the-batch-account"></a>Batch 帐户的组件

使用 Batch 帐户，可以在 Azure 中高效运行大规模并行和高性能计算 (HPC) 批处理作业。 在该帐户中，可以管理以下内容：

- 正在运行的应用程序

- 池和池中节点的分配

- 任务的数量和类型 

- 数据的输入和输出。 无需安装其他软件来管理任务。

- 创建 Batch 帐户时，系统将要求你为其分配一个名称。 此名称是其 ID，分配后无法更改。

- 若要更改帐户的名称，需要将其删除并创建新的 Batch 帐户。

- 该帐户是在要使用的订阅中创建的。

- 使用该帐户来识别和检索订阅中的任何 Batch 帐户的主要和次要帐户密钥。

- 该帐户维护有关池分配和核心配额的信息。  

- 该帐户包含位置信息。

- 该帐户识别存储帐户。

## <a name="next-steps"></a>后续步骤

- 使用 [Azure 门户](batch-account-create-portal.md)创建 Batch 帐户。
- 以编程方式创建 Batch 帐户，如使用 [Batch 管理 .NET 库](batch-management-dotnet.md)。
- [配置或禁用对 Azure Batch 池中计算节点的远程访问](pool-endpoint-configuration.md)。
- [在 Batch 计算节点上运行作业准备和作业发布任务](batch-job-prep-release.md)

