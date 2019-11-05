---
title: 向现有 Azure 数据共享添加数据集
description: 了解如何向 Azure 数据共享中的现有数据共享添加数据集，以及如何与相同的收件人共享数据集。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 00c96950565b077e65f84e2d8b4977092df5e317
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490551"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>如何将数据集添加到 Azure 数据共享中的现有共享

本文介绍如何使用 Azure 数据共享向预先存在的数据共享添加数据集。 这样，就可以与相同的收件人共享更多的数据，而不必创建新的共享。

有关如何在创建共享时添加数据集的信息，请参阅[共享数据](share-your-data.md)教程。

## <a name="navigate-to-a-sent-data-share"></a>导航到已发送的数据共享

在 Azure 数据共享中，导航到已发送的共享并选择 "**数据集**" 选项卡。单击 " **+ 添加数据集**" 按钮以添加更多数据集。

![添加数据集](./media/how-to/how-to-add-datasets/add-datasets.png)

在右侧面板中，选择要添加的数据集类型，然后单击 "**下一步**"。 选择要添加的数据的订阅和资源组。 使用下拉箭头，查找并选中要添加的数据旁边的复选框。

![添加数据集](./media/how-to/how-to-add-datasets/add-datasets-side.png)

单击 "**添加数据集**" 后，这些数据集将添加到你的共享中。 注意：快照必须由使用者触发，才能让其查看新的数据集。 如果已配置快照设置，则在下一个计划的快照完成后，使用者将看到新的数据集。 如果未配置快照设置，则使用者必须手动触发数据的完整副本或增量副本才能接收更新。 有关快照的详细信息，请参阅[快照](terminology.md)。

## <a name="next-steps"></a>后续步骤
了解有关如何[将收件人添加到现有数据共享的](how-to-add-recipients.md)详细信息。