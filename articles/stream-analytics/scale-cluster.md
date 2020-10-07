---
title: 重设 Azure 流分析群集的大小
description: 了解如何纵向扩展和缩减 Azure 流分析群集的大小。
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 0763e56de6c72a36d39b17d153db6fc4d7dd821a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943993"
---
# <a name="resize-an-azure-stream-analytics-cluster"></a>重设 Azure 流分析群集的大小

流分析群集的容量以流单元 (SU) 来度量。 可以在同一群集中并行运行多个作业，只要分配给所有正在运行的作业的 SU 总和不超过群集容量。

可以纵向扩展和缩减群集容量，以匹配流工作负载的大小。 缩放群集需要时间，因此不应频繁进行缩放。 建议计划和预配一个群集，其中包含要使用的准确数量的 SU。

## <a name="change-the-scale-of-your-cluster"></a>更改群集的规模

1. 在 Azure 门户中，找到并选择流分析群集。

1. 在“概述”部分，选择“规模” 。 可以看到分配给群集的 SU 数量。 根据需要使用选择器增加或减少 SU。

   ![缩放群集](./media/scale-cluster/scale-cluster.png)

缩放操作不会影响当前正在运行的任何作业。

## <a name="next-steps"></a>后续步骤

现在你已了解如何纵向扩展和缩减流分析群集。 接下来，你可以了解如何管理专用终结点和自动缩放作业：

* [在 Azure 流分析群集中管理专用终结点](private-endpoints.md)
* [在 Azure 流分析群集中管理作业](manage-jobs-cluster.md)
