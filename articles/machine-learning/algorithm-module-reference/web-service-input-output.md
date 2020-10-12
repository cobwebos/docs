---
title: Web 服务输入/输出：模块参考
description: 了解 Azure 机器学习设计器中的 web 服务模块
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: a62f8aee0bd0a0d2b7009a48e9d5f00ea3c5155f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90883216"
---
# <a name="web-service-input-and-web-service-output-modules"></a>Web 服务输入和 Web 服务输出模块

本文介绍 Azure 机器学习设计器中的 Web 服务输入和 Web 服务输出模块。

Web 服务输入模块只能连接到类型为 DataFrameDirectory 的输入端口****。 Web 服务输出模块只能从类型为 DataFrameDirectory 的输出端口进行连接****。 可以在“Web 服务”类别下的模块树中找到这两个模块****。 

Web 服务输入模块指示用户数据进入管道的位置。 Web 服务输出模块指示实时推理管道中返回用户数据的位置。

## <a name="how-to-use-web-service-input-and-output"></a>如何使用 Web 服务输入和输出

从训练管道[创建实时推理管道](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline)时，将自动添加 Web 服务输入和 Web 服务输出模块，以显示用户数据进入管道的位置和数据返回的位置。 

> [!NOTE]
> 自动生成实时推理管道是基于规则的尽力而为的过程。 无法保证正确性。 

若要满足你的需求，可以手动添加 Web 服务输入和 Web 服务输出模块或将其删除。 确保实时推理管道至少有一个 Web 服务输入模块和一个 Web 服务输出模块。 如果有多个 Web 服务输入或 Web 服务输出模块，请确保它们具有唯一的名称。 可以在模块的右侧面板中输入名称。

还可以通过将 Web 服务输入和 Web 服务输出模块添加到未提交的管道中来手动创建实时推理管道。

> [!NOTE]
> 管道类型将在首次提交时确定。 在首次提交之前，请确保添加 Web 服务输入和 Web 服务输出模块。

下面的示例演示如何从“执行 Python 脚本”模块手动创建实时推理管道。 

![示例](media/module/web-service-input-output-example.png)
   
提交管道并成功完成运行之后，可以部署实时终结点。
   
> [!NOTE]
>  在前面的示例中，“手动输入数据”为 Web 服务输入提供了数据架构，并且对于部署实时终结点是必需的****。 通常，应始终将模块或数据集连接到 Web 服务输入连接的端口，以提供数据架构****。
   
## <a name="next-steps"></a>后续步骤
详细了解[部署实时终结点](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint)。

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。