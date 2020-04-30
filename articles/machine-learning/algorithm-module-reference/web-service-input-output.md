---
title: Web 服务输入/输出
description: 了解 Azure 机器学习设计器中的 web 服务模块（预览）
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81462082"
---
# <a name="web-service-inputoutput"></a>Web 服务输入/输出

本文介绍 Azure 机器学习设计器（预览版）中的 " **Web 服务输入**模块" 和 " **web 服务输出**" 模块。

**Web 服务输入**模块只能连接到类型为**DataFrameDirectory**的输入端口。 和**Web 服务输出**模块只能从类型为**DataFrameDirectory**的输出端口连接。 可在模块树中的 " **Web 服务**" 类别下找到这两个模块。 

**Web 服务输入**模块用于指示用户数据进入管道的位置， **web 服务输出**模块用于指示用户数据在实时推理管道中的返回位置。

## <a name="how-to-use-web-service-inputoutput"></a>如何使用 Web 服务输入/输出

- 当你从定型管道创建实时推理管道时，将自动添加**Web 服务输入**和**web 服务输出**模块，以显示用户数据进入管道的位置和返回数据的位置。 

    详细了解如何[创建实时推理管道](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline)。

    > [!NOTE]
    > 自动生成实时推理管道是一项基于规则的尽力操作过程，不保证正确性。 你可以手动添加或删除**Web 服务输入/输出**模块，以满足你的要求。 请确保实时推理管道中至少有一个**Web 服务输入**模块和一个**web 服务输出**模块。 如果有多个**Web 服务输入**或**web 服务输出**模块，请确保它们具有唯一名称，您可以在模块的右面板中输入名称。

- 还可以通过将**Web 服务输入**和**web 服务输出**模块添加到未提交的管道来手动创建实时推理管道。

    > [!NOTE]
    >  管道类型将在第一次提交时确定。 如果要创建实时推理管道，请确保在首次提交前添加**Web 服务输入**和**web 服务输出**模块。

   下面的示例演示如何从**执行 Python 脚本**模块手动创建实时推理管道。 

   ![示例](media/module/web-service-input-output-example.png)
   
   提交管道并成功完成运行后，你将能够部署实时终结点。
   
   > [!NOTE]
   >  在上面的示例中，**手动输入数据**为 web 服务输入提供数据架构，并是部署实时终结点所必需的。 通常，应始终将模块或数据集连接到**Web 服务输入**连接到的端口，以提供数据架构。
   
## <a name="next-steps"></a>后续步骤
详细了解如何[部署实时终结点](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint)。
参阅 Azure 机器学习[可用的模块集](module-reference.md)。