---
title: Web 服务输入/输出
description: 了解 Azure 机器学习设计器中的 Web 服务模块（预览版）
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462082"
---
# <a name="web-service-inputoutput"></a>Web 服务输入/输出

本文介绍 Azure 机器学习设计器（预览）中的**Web 服务输入**模块和**Web 服务输出**模块。

**Web 服务输入**模块只能连接到类型**为 DataFrameDirectory**的输入端口。 **并且 Web 服务输出**模块只能从具有**类型 DataFrameDirectory**的输出端口进行连接。 这两个模块可以在模块树中找到，在**Web 服务**类别下。 

**Web 服务输入**模块用于指示用户数据进入管道的位置 **，Web 服务输出**模块用于指示在实时推理管道中返回用户数据的位置。

## <a name="how-to-use-web-service-inputoutput"></a>如何使用 Web 服务输入/输出

- 当您从训练管道创建实时推理管道时，将自动添加**Web 服务输入**和**Web 服务输出**模块，以显示用户数据进入管道的位置以及返回数据的位置。 

    详细了解[如何创建实时推理管道](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline)。

    > [!NOTE]
    > 自动生成实时推理管道是一个基于规则的尽力过程，不能保证正确性。 您可以手动添加或删除 Web**服务输入/输出**模块，以满足您的要求。 确保实时推理管道中至少有一个**Web 服务输入**模块和一个**Web 服务输出**模块。 如果您有多个**Web 服务输入**或**Web 服务输出**模块，请确保它们具有唯一名称，您可以在模块的右侧面板中输入该名称。

- 还可以通过将**Web 服务输入**模块和**Web 服务输出**模块添加到未提交的管道来手动创建实时推理管道。

    > [!NOTE]
    >  管道类型将在您第一次提交时确定。 因此，如果要创建实时推理管道，请确保在首次提交之前添加**Web 服务输入**和**Web 服务输出**模块。

   下面的示例演示如何从**执行 Python 脚本**模块手动创建实时推理管道。 

   ![示例](media/module/web-service-input-output-example.png)
   
   提交管道并成功完成运行后，您将能够部署实时终结点。
   
   > [!NOTE]
   >  在上面的示例中 **，输入数据手动**提供 Web 服务输入的数据架构，是部署实时终结点所必需的。 通常，应始终将模块或数据集连接到**Web 服务输入**连接的端口以提供数据架构。
   
## <a name="next-steps"></a>后续步骤
详细了解[如何部署实时终结点](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint)。
参阅 Azure 机器学习[可用的模块集](module-reference.md)。