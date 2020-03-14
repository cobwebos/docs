---
title: 管理你的帐户-Azure Batch |Microsoft Docs
description: 了解什么是 Azure Batch 帐户
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
ms.openlocfilehash: 72630a2003b63e60ba79882e1861283173840425
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375182"
---
# <a name="manage-your-batch-account"></a>管理 Batch 帐户

批处理帐户是批处理服务中唯一标识的实体。 所有处理都与一个 Batch 帐户相关联。

可以通过 [Azure 门户](batch-account-create-portal.md)或编程方式（例如使用[批处理管理 .NET 库](batch-management-dotnet.md)）创建 Azure Batch 帐户。 创建该帐户时，可以关联一个 Azure 存储帐户，用于存储与作业相关的输入和输出数据或应用程序。

可以在单个批处理帐户中运行多个批处理工作负荷，或者在相同订阅的不同 Azure 区域的批处理帐户之间分散工作负荷。

## <a name="components-of-the-batch-account"></a>Batch 帐户的组件

Batch 帐户允许在 Azure 中高效运行大规模并行和高性能计算（HPC） Batch 作业。 在你管理的帐户中：

- 正在运行的应用程序

- 池和池中的节点分配

- 任务的数目和类型 

- 数据的输入和输出。 不需要安装其他软件来管理任务。

- 创建批处理帐户时，系统将要求你为该帐户分配一个名称。 此名称是其 ID，分配后无法更改。

- 若要更改帐户的名称，需要将其删除并创建新的 Batch 帐户。

- 此帐户是在要使用的订阅中创建的。

- 使用该帐户从你的订阅中的任何 Batch 帐户标识并检索主要和辅助帐户密钥。

- 帐户维护有关池分配和核心配额的信息。  

- 帐户包含位置信息。

- 帐户用于标识存储帐户。

## <a name="next-steps"></a>后续步骤

- 使用[Azure 门户](batch-account-create-portal.md)创建批处理帐户。
- 以编程方式创建批处理帐户，如使用[批处理管理 .net 库](batch-management-dotnet.md)。
- [配置或禁用对 Azure Batch 池中计算节点的远程访问](pool-endpoint-configuration.md)。
- [在 Batch 计算节点上运行作业准备和作业释放任务](batch-job-prep-release.md)
