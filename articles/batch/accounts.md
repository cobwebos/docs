---
title: Batch 帐户和 Azure 存储帐户
description: 了解 Azure Batch 帐户以及如何从开发角度使用它们。
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 20a2a28d0eaa2c7997ea93e66d07ecb99bf297a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83790904"
---
# <a name="batch-accounts-and-azure-storage-accounts"></a>Batch 帐户和 Azure 存储帐户

Azure Batch 帐户是 Batch 服务中唯一标识的实体。 大多数 Batch 解决方案使用 [Azure 存储](../storage/index.yml)来存储资源文件和输出文件，因此，每个批处理帐户通常与相应的存储帐户相关联。

## <a name="batch-accounts"></a>批处理帐户

所有处理和资源都与一个 Batch 帐户相关联。 当应用程序针对 Batch 服务提出请求时，会使用 Azure Batch 帐户名称、帐户的 URL 以及访问密钥或 Azure Active Directory 令牌对请求进行身份验证。

可以在单个 Batch 帐户中运行多个 Batch 工作负荷。 还可以在相同订阅的不同 Azure 区域的 Batch 帐户之间分散工作负荷。

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

可以通过 [Azure 门户](batch-account-create-portal.md)或编程方式（例如使用[批处理管理 .NET 库](batch-management-dotnet.md)）创建 Batch 帐户。 创建该帐户时，可以关联一个 Azure 存储帐户，用于存储与作业相关的输入和输出数据或应用程序。

## <a name="azure-storage-accounts"></a>Azure 存储帐户

大多数 Batch 解决方案使用 Azure 存储来存储资源文件和输出文件。 例如，Batch 任务（包括标准任务、启动任务、作业准备任务和作业释放任务）通常指定位于存储帐户中的资源文件。 存储帐户还会存储处理的数据和生成的任何输出数据。

Batch 支持以下类型的 Azure 存储帐户：

- 常规用途 v2 (GPv2) 帐户
- 常规用途 v1 (GPv1) 帐户
- Blob 存储帐户（目前支持虚拟机配置中的池）

有关存储帐户的详细信息，请参阅 [Azure 存储帐户概述](../storage/common/storage-account-overview.md)。

创建 Batch 帐户时可以将存储帐户与 Batch 帐户关联，也可以稍后关联。 选择存储帐户时，请考虑成本和性能要求。 例如，与 GPv1 相比，GPv2 和 blob 存储帐户选项支持更大的[容量和可伸缩性限制](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)。 （请联系 Azure 支持以请求提高存储上限。）对于包含大量读取或写入存储帐户的并行任务的 Batch 解决方案，这些帐户选项可以提高其性能。

## <a name="next-steps"></a>后续步骤

- 了解[节点和池](nodes-and-pools.md)。
- 了解如何使用 [Azure 门户](batch-account-create-portal.md)创建 Batch 帐户。
