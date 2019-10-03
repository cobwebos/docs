---
title: include 文件
description: include 文件
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/06/2019
ms.openlocfilehash: eb84dc1b5bf3f756e484ef27aaa998ab6b94cc51
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891637"
---
试验在计算目标（附加到工作区的计算资源）上运行。  一旦创建了计算目标，就可以在以后的运行中重用它。

1. 选择底部的“运行”，运行此试验。 

1. 出现“设置计算目标”  对话框时，如果你的工作区已有计算资源，现在就可以选择它。  否则，请选择“新建”  。

    > [!NOTE]
    > 可视界面只能对机器学习计算目标运行试验。 不会显示其他计算目标。

1. 为计算资源提供名称。

1. 选择“运行”。 

    ![设置计算目标](./media/aml-ui-create-training-compute/set-compute.png)

    现在将创建计算资源。 在试验右上角查看状态。 

    > [!NOTE]
    > 创建计算资源大约需要 5 分钟。 创建资源之后，可以重用它，并跳过此等待时间，以便将来运行。
    >
    > 计算资源在空闲时将自动缩放为 0 个节点以节省成本。  在延迟之后再次使用它时，可能会再次经历大约 5 分钟的等待时间，同时它会重新扩展。
