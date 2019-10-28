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
ms.openlocfilehash: 3f969fb346ce341e694458dcc38bf193f012226b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792660"
---
管道在计算目标上运行，该目标是附加到工作区的计算资源。 一旦创建了计算目标，就可以在以后的运行中重用它。

1. 选择画布顶部的“运行”  以运行管道。

1. 当“设置”  窗格出现时，选择“选择计算目标”  。

    如果已有可用的计算目标，则可以选择它来运行此管道。

    > [!NOTE]
    > 可视界面只能对机器学习计算目标运行试验。 不会显示其他计算目标。

1. 为计算资源提供名称。

1. 选择“保存”。 

    ![设置计算目标](./media/aml-ui-create-training-compute/set-compute.png)

1. 选择“运行”。 

1. 在“设置管道运行”  对话框中，为**试验**选择“+ 新建试验” 

    > [!NOTE]
    > 试验将相似的管道运行组合在一起。 如果多次运行管道，则可以选择相同的试验进行连续运行。

    * 输入描述性**试验名称**

    * 选择“运行” 
    
    可以在画布的右上角查看运行状态和详细信息。

    > [!NOTE]
    > 创建计算资源大约需要 5 分钟。 创建资源之后，可以重用它，并跳过此等待时间，以便将来运行。
    >
    > 计算资源在空闲时将自动缩放为 0 个节点以节省成本。  在延迟之后再次使用它时，可能会再次经历大约 5 分钟的等待时间，同时它会重新扩展。
