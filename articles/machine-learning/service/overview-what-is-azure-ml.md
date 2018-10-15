---
title: 什么是 Azure 机器学习服务？
description: 解释云中机器学习的基本概念，介绍其作用，以及定义机器学习的术语。 Azure 机器学习概述 - 这是一个集成式的端到端数据科学解决方案，能够让专业数据科学家以云规模开发、试验和部署高级分析应用程序。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 09/24/2018
ms.openlocfilehash: 660e5515b765e133d5bc9309be5439209b957353
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238498"
---
# <a name="what-is-azure-machine-learning-service-preview"></a>什么是 Azure 机器学习服务（预览版）？

Azure 机器学习服务（预览版）是一款可用于开发和部署机器学习模型的云服务。 使用 Azure 机器学习服务，你可以在生成、培训、部署和管理模型时对其进行跟踪，所有这些都以云提供的广泛规模完成。

## <a name="what-is-machine-learning"></a>什么是机器学习？

机器学习是一项数据科研技术，可以让计算机根据现有的数据来预测将来的行为、结果和趋势。 使用机器学习，计算机可以在不需显式编程的情况下进行学习。

机器学习的预测可让应用和设备变得更聪明。 例如，在网上购物时，机器学习可根据你购买的产品帮助推荐其他产品。 或者，在刷信用卡时，机器学习可将这笔交易与交易数据库进行比较，帮助检测诈骗。 当吸尘器机器人打扫房间时，机器学习可帮助它确定作业是否已完成。

## <a name="what-is-azure-machine-learning-service"></a>什么是 Azure 机器学习服务？

Azure 机器学习服务提供了一个基于云的环境，你可以使用这一环境来开发、培训、测试、部署、管理和跟踪机器学习模型。

[ ![Azure 机器学习服务工作流](./media/overview-what-is-azure-ml/aml.png) ] (./media/overview-what-is-azure-ml/aml.png#lightbox)

Azure 机器学习服务完全支持开放源代码技术，因此，你可以使用数以万计的开放源代码 Python 包与机器学习组件（如 TensorFlow 和 scikit-learn）。
借助丰富的工具（如 [Jupyter Notebook](http://jupyter.org) 或 [Visual Studio Code Tools for AI](https://visualstudio.microsoft.com/downloads/ai-tools-vscode/)），可以交互式地轻松探索数据、转换数据，然后开发和测试模型。
此外，Azure 机器学习服务还包括[自动化模型生成和优化](tutorial-auto-train-models.md)的功能，能够帮助你轻松、高效和准确地创建模型。

使用 Azure 机器学习服务，你可以先在本地计算机上开始培训，然后扩大到云。 通过提供对 [Azure Batch AI](https://azure.microsoft.com/services/batch-ai/) 的本机支持和[高级超参数优化服务](how-to-tune-hyperparameters.md)，你可以使用云的强大功能更快地生成更好的模型。 

在拥有合适的模型后，即可轻松地将其部署在 Docker 等的容器中。 这意味着，可以轻松部署到 [Azure 容器实例](how-to-deploy-to-aci.md)或 [Azure Kubernetes 服务](how-to-deploy-to-aks.md)，或者，你可以在自己的部署中使用容器，无论是在本地还是在云中。
在尝试寻找最佳解决方案时，可以管理已部署的模型并跟踪多个运行。

[!INCLUDE [aml-preview-note](../../../includes/aml-preview-note.md)]

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>通过 Azure 机器学习服务，我可以执行哪些操作？

Azure 机器学习服务可以自动生成模型并为你自动优化模型。
有关示例，请参阅[教程：使用 Azure 自动机器学习自动定型分类模型](tutorial-auto-train-models.md)。

或者，可以使用适用于 Python 的 Azure 机器学习 <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a>，以及开放源代码 Python 包，在 Azure 机器学习服务工作区中自行生成和训练高度准确的机器学习和深度学习模型。
可以从开放源代码 Python 包中提供的许多机器学习组件中进行选择，例如：

- <a href="http://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- CNTK<a href="https://www.microsoft.com/cognitive-toolkit/" target="_blank"></a>
- <a href="http://mxnet.io" target="_blank">MXNet</a>

拥有模型后，你就可以使用它来创建可以在本地部署以进行测试的容器（如 Docker），然后将其作为 [Azure 容器实例](how-to-deploy-to-aci.md)或 [Azure Kubernetes 服务](how-to-deploy-to-aks.md)中的生产 Web 服务。

然后，可以使用 [Azure 门户](https://portal.azure.com/)或 [Azure 机器学习 CLI 扩展](https://review.docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)来管理已部署的模型。
你可以在跟踪模型的试验的同时评估模型指标、重新训练和重新部署模型的新版本。

要开始使用 Azure 机器学习服务，请参阅下面的[后续步骤](#next-steps)。

## <a name="how-is-azure-machine-learning-service-different-from-studio"></a>Azure 机器学习服务与 Studio 有何不同？

Azure 机器学习工作室是一个协作式拖放可视化工作区，无需编写代码即可生成、测试和部署机器学习解决方案。 它使用预构建和预配置的机器学习算法和数据处理模块。

如果想要快速轻松地试验机器学习模型，请使用机器学习工作室，并且内置的机器学习算法足以供你的解决方案使用。

如果你在 Python 环境中工作并且希望更好地控制机器学习算法，或者你想使用开放源代码机器学习库，那么机器学习服务将是你的最佳选择。

> [!NOTE]
> Azure 机器学习服务无法部署或管理在 Azure 机器学习工作室中创建的模型。

## <a name="free-trial"></a>免费试用
如果不是订户，可以[免费建立一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)。 你将获得可用于 Azure 服务的额度。 信用额度用完后，可以保留该帐户并继续使用[免费的 Azure 服务](https://azure.microsoft.com/free/)。 除非显式更改设置并要求付费，否则不会对信用卡收取任何费用。 也可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)：MSDN 订阅每月提供可用于试用付费版 Azure 服务的信用额度。

## <a name="next-steps"></a>后续步骤

- 按照文章[使用 Azure 门户入门](quickstart-get-started.md)创建机器学习服务工作区
 
- 按照完整教程[使用 Azure 机器学习定型图像分类模型](tutorial-train-models-with-aml.md)，了解如何使用 Azure 机器学习服务训练和部署模型

- 有关允许 Azure 机器学习自动生成和自动优化模型的信息，请参阅[教程：使用 Azure 自动机器学习自动定型分类模型](tutorial-auto-train-models.md)

- 有关该服务技术方面的深入探讨，请参阅 [Azure 机器学习服务体系结构和概念](concept-azure-machine-learning-architecture.md)

- 有关 Microsoft 提供的其他机器学习产品的更多信息，请参阅 [Microsoft 的其他机器学习产品](./overview-more-machine-learning.md)


<!-- 

An intro to AML or an end-to-end quickstart video could go here.

In this 9-minute video, learn how you can benefit your app. You'll learn about key features and what a typical workflow looks like. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minutes covers key features and use-cases.
+ 3-4 minutes covers service provisioning. 
+ 4-6 minutes covers Import Data wizard used to create an index using the built-in real estate dataset.

-->
