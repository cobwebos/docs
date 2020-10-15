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
ms.openlocfilehash: 72eea7c46dd005cd16ae5b8f0022c1174dd28f27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89667484"
---
# <a name="orchestrate-with-pipelines"></a>编排管道

在本教程中，你将了解如何使用 Synapse Studio 编排管道和活动。 

## <a name="overview"></a>概述

可以在 Azure Synapse 中安排各种任务。

1. 在 Synapse Studio 中，转到“安排”中心。
1. 选择 + > “管道”以创建新管道 。
1. 转到“开发”中心并选择之前创建的其中一个笔记本。
1. 将该笔记本拖动到管道。
1. 在管道中，选择“添加触发器” > “新建/编辑” 。
1. 在“选择触发器”中，选择“新建”，然后在“定期”中，将触发器设置为每 1 小时运行一次  。
1. 选择“确定”。
1. 选择“全部发布”。 管道每小时运行一次。
1. 若要管道立即运行而不等待一个小时，请选择“添加触发器” > “新建/编辑” 。



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Power BI 的可视化数据](get-started-visualize-power-bi.md)
                                 
