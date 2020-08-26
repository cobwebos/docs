---
title: 教程：开始编排管道
description: 在本教程中，你将了解如何使用 Synapse Studio 编排管道和活动。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 5e32a6a9817f2a3176e96e39c5e261875e8f4ed1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093222"
---
# <a name="orchestrate-with-pipelines"></a>编排管道

在本教程中，你将了解如何使用 Synapse Studio 编排管道和活动。 

## <a name="overview"></a>概述

可以在 Azure Synapse 中安排各种任务。

1. 在 Synapse Studio 中，转到“安排”中心。
1. 选择 + > “管道”以创建新管道 。
1. 转到“开发”中心并找到之前创建的笔记本。
1. 将该笔记本拖动到管道。
1. 在管道中，选择“添加触发器” > “新建/编辑” 。
1. 在“选择触发器”中，选择“新建”，然后在“定期”中，将触发器设置为每 1 小时运行一次  。
1. 选择“确定”。
1. 选择“全部发布”。 管道每小时运行一次。
1. 若要管道立即运行而不等待一个小时，请选择“添加触发器” > “新建/编辑” 。



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Power BI 的可视化数据](get-started-visualize-power-bi.md)
                                 
