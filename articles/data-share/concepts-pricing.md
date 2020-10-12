---
title: 了解 Azure 数据共享定价
description: 了解 Azure 数据共享价格的工作原理
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 1c2c58e206a70a3801c712df3b5582409712d3c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88136756"
---
# <a name="understand-azure-data-share-pricing"></a>了解 Azure 数据共享定价

对于基于快照的共享，为数据集快照和快照执行提供 Azure 数据共享费用。 本文介绍如何使用快照历史记录信息估算数据集快照和快照执行。 当前，数据访问接口针对数据集快照和快照执行进行计费。

## <a name="dataset-snapshot"></a>数据集快照

数据集快照是将数据集从源移动到目标的操作。 为共享拍摄快照时，共享中每个数据集的快照都是数据集快照操作。 按照以下步骤查看数据集快照的列表。 

1. 在 Azure 门户中，导航到数据共享资源。

1. 从 "已发送共享" 或 "已接收共享" 中选择一个共享。

1. 单击 " **历史记录** " 选项卡。

1. 单击快照的 "开始时间"。
 
    ![快照历史记录](./media/concepts/concepts-pricing/pricing-snapshot-history.png "快照历史记录") 

1. 查看数据集快照操作的列表。 每个行项对应于一个数据集快照操作。 在此示例中，有两个数据集快照。

    ![数据集快照操作](./media/concepts/concepts-pricing/pricing-dataset-snapshot.png "数据集快照操作")

## <a name="snapshot-execution"></a>快照执行

快照执行包括将数据集从源移动到目标所需的资源。 对于每个数据集快照操作，快照执行的计算方式为 Vcore 的数量乘以快照持续时间。 费用按分钟按比例舍入。 基于源目标对和数据模式动态选择 vCore 的数目。 按照以下步骤查看用于数据集快照操作的快照开始时间、结束时间和 Vcore。

1. 在 Azure 门户中，导航到数据共享资源。

1. 从 "已发送共享" 或 "已接收共享" 中选择一个共享。

1. 单击 " **历史记录** " 选项卡。

1. 单击快照的 "开始时间"。

    ![快照历史记录](./media/concepts/concepts-pricing/pricing-snapshot-history.png "快照历史记录") 

1. 单击数据集快照操作的 "状态"。

    ![数据集快照状态](./media/concepts/concepts-pricing/pricing-snapshot-status.png "数据集快照状态")

1. 查看用于此数据集快照操作的开始时间、结束时间和 Vcore。 

    ![快照执行](./media/concepts/concepts-pricing/pricing-snapshot-execution.png "快照执行")

## <a name="next-steps"></a>后续步骤

- 获取最新定价信息- [Azure 数据共享定价](https://azure.microsoft.com/pricing/details/data-share/)
- 使用 Azure 定价计算器估算成本- [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)
