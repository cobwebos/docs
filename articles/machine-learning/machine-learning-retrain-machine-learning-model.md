---
title: "重新训练机器学习模型 | Microsoft Docs"
description: "了解如何重新训练模型并更新 Web 服务以使用 Azure 机器学习中的最新训练模型。"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: d1cb6088-4f7c-4c32-94f2-f7523dad9059
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-donglo
translationtype: Human Translation
ms.sourcegitcommit: 201b07536bcee58e2b7102379dff1c1c93c4b675
ms.openlocfilehash: 882157d2cb544e5bb59caf7d64de579e23b12480


---
# <a name="retrain-a-machine-learning-model"></a>重新训练机器学习模型
作为 Azure 机器学习中机器学习模型操作过程的一部分，你的模型被训练并保存。 使用它来创建预测 Web 服务。 然后就可以在网站、仪表板和移动应用中使用 Web 服务。 

使用机器学习创建的模型通常不是静态的。 当新数据变得可用时，或当 API 使用者拥有其自己的数据时，需要重新训练模型。 

重新训练可能会经常发生。 使用“编程重新训练 API”功能，你可以使用“重新训练 API”以编程方式重新训练模型，并使用新训练的模型更新 Web 服务。 

本文档教室重新训练过程，并展示如何使用重新训练 API。

## <a name="why-retrain-defining-the-problem"></a>为何重新训练：定义问题
作为机器学习训练过程的一部分，将使用一组数据训练模型。 使用机器学习创建的模型通常不是静态的。 当新数据变得可用时，或当 API 使用者拥有其自己的数据时，需要重新训练模型。

在这些情况下，编程 API 提供了一种便捷的方法，允许你或你的 API 的使用者创建一个客户端，可以一次性或定期地使用自己的数据重新训练模型。 然后，他们可以评估重新训练的结果，并更新 Web 服务 API 以使用新训练的模型。

> [!NOTE]
> 如果你有现有的训练实验和新 Web 服务，建议你检查重新训练现有预测 Web 服务，而不要按照下一节中提到的步骤。
> 
> 

## <a name="end-to-end-workflow"></a>端到端工作流
该过程包括以下组件：作为 Web 服务发布的训练实验和预测实验。 若要启用已训练模型的重新训练，训练实验必须作为具有训练模型输出的 Web 服务发布。 这使 API 能够访问模型以进行重新训练。 

以下步骤适用于新版和经典 Web 服务：

创建初始预测 Web 服务：

* 创建训练实验
* 创建预测 Web 实验
* 部署预测 Web 服务

重新训练 Web 服务：

* 更新训练实验以允许重新训练
* 部署重新训练 Web 服务
* 使用 Batch 执行服务代码重新训练模型

有关上述步骤的演练，请参阅[以编程方式重新训练机器学习模型](machine-learning-retrain-models-programmatically.md)。

如果已部署了经典 Web 服务：

* 在预测 Web 服务上创建新终结点
* 获取 PATCH URL 和代码
* 使用 PATCH URL 将新终结点指向重新训练模型 

有关上述步骤的演练，请参阅[重新训练经典 Web 服务](machine-learning-retrain-a-classic-web-service.md)。

如果在重新训练经典 Web 服务时遇到困难，请参阅 [Azure 机器学习经典 Web 服务重新训练的故障排除](machine-learning-troubleshooting-retraining-models.md)。

如果已部署了新的 Web 服务：

* 登录到 Azure Resource Manager 帐户
* 获取 Web 服务定义
* 将 Web 服务定义导出为 JSON
* 在 JSON 中更新对 `ilearner` blob 的引用
* 将 JSON 导入到 Web 服务定义中
* 使用新的 Web 服务定义更新 Web 服务

有关上述步骤的演练，请参阅[使用机器学习管理 PowerShell cmdlet 重新训练新 Web 服务](machine-learning-retrain-new-web-service-using-powershell.md)。

为经典 Web 服务设置重新训练的过程包括以下步骤：

![重新训练过程概述][1]

为新 Web 服务设置重新训练的过程包括以下步骤：

![重新训练过程概述][7]

## <a name="other-resources"></a>其他资源
* [使用 Azure 数据工厂重新训练和更新 Azure 机器学习模型](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
* [使用 PowerShell 从一个实验中创建多个机器学习模型和 Web 服务终结点](machine-learning-create-models-and-endpoints-with-powershell.md)
* [使用 API 的 AML 重新训练模型](https://www.youtube.com/watch?v=wwjglA8xllg)视频介绍如何使用重新训练 API 和 PowerShell 重新训练在 Azure 机器学习中创建的机器学习模型。

<!--image links-->
[1]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png




<!--HONumber=Jan17_HO2-->


