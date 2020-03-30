---
title: 管理您的帐户 - Azure 批处理 |微软文档
description: 了解 Azure 批处理帐户的组成内容
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4df2ec0439e659fd8dc1448c6209c9718114791
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479784"
---
# <a name="manage-your-batch-account"></a>管理您的批次帐户

批处理帐户是批处理服务中唯一标识的实体。 所有处理都与一个 Batch 帐户相关联。

可以通过 [Azure 门户](batch-account-create-portal.md)或编程方式（例如使用[批处理管理 .NET 库](batch-management-dotnet.md)）创建 Azure Batch 帐户。 创建该帐户时，可以关联一个 Azure 存储帐户，用于存储与作业相关的输入和输出数据或应用程序。

可以在单个批处理帐户中运行多个批处理工作负荷，或者在相同订阅的不同 Azure 区域的批处理帐户之间分散工作负荷。

## <a name="components-of-the-batch-account"></a>批处理帐户的组件

Batch 帐户使您能够在 Azure 中高效地运行大规模并行和高性能计算 （HPC） 批处理作业。 在您管理的帐户中：

- 正在运行的应用程序

- 池内池和节点的分配

- 任务的数量和类型 

- 数据的输入和输出。 您无需安装其他软件来管理任务。

- 创建 Batch 帐户时，系统会要求您为其分配名称。 此名称是其 ID，分配后无法更改。

- 要更改帐户的名称，您需要将其删除并创建新的 Batch 帐户。

- 帐户是在要使用的订阅中创建的。

- 使用 该帐户从订阅中的任何 Batch 帐户标识和检索主帐户密钥和辅助帐户密钥。

- 该帐户维护有关池分配和核心配额的信息。  

- 该帐户包含位置信息。

- 该帐户标识您的存储帐户。

## <a name="next-steps"></a>后续步骤

- 使用[Azure 门户](batch-account-create-portal.md)创建批处理帐户。
- 以编程方式创建批处理帐户，例如使用[批处理管理 .NET 库](batch-management-dotnet.md)。
- [配置或禁用对 Azure 批处理池中的计算节点的远程访问](pool-endpoint-configuration.md)。
- [在 Batch 计算节点上运行作业准备和作业发布任务](batch-job-prep-release.md)

