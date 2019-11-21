---
title: include 文件
description: include 文件
services: machine-learning
author: peterclu
ms.service: machine-learning
ms.author: peterlu
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/09/2019
ms.openlocfilehash: 1ee90e0c99234497b072bbee0b92d76129baea48
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929663"
---
管道在计算目标上运行，该目标是附加到工作区的计算资源。 创建计算目标后，就可以在以后的运行中重用它。

1. 选择画布顶部的“运行”  以运行管道。

1. 当“设置”  窗格出现时，选择“选择计算目标”  。

    如果已有可用的计算目标，则可以选择它来运行此管道。

    > [!NOTE]
    > 设计器只能对机器学习计算目标运行试验。 不会显示其他计算目标。

1. 输入计算资源的名称。

1. 选择“保存”。 

    ![设置计算目标](./media/aml-ui-create-training-compute/set-compute.png)

1. 选择“运行”。 

1. 在“设置管道运行”  对话框中，为“试验”  选择“+ 新建试验”  。

    > [!NOTE]
    > 试验将相似的管道运行组合在一起。 如果多次运行管道，则可以选择相同的试验进行连续运行。

    1. 输入描述性名称“试验名称”  。

    1. 选择“运行”。 
    
    可以在画布的右上角查看运行状态和详细信息。

    > [!NOTE]
    > 创建计算资源大约需要五分钟。 创建资源之后，可以重用它，并跳过此等待时间，以便将来运行。
    >
    > 计算资源在空闲时会自动缩放为 0 个节点以节省成本。 在延迟之后再次使用它时，可能会经历大约五分钟的等待时间，同时它会重新扩展。
