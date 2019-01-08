---
title: Machine Learning Workbench 有何遭遇？
titleSuffix: Azure Machine Learning service
description: 了解 Machine Learning Workbench 应用程序有何遭遇、Azure 机器学习服务有何变化，以及支持时间线是什么。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: ff6b61874363bbc869bd509174e58640a2487f56
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811301"
---
# <a name="whats-happening-to-machine-learning-workbench-in-azure-machine-learning-service"></a>Azure 机器学习服务中的 Machine Learning Workbench 有何遭遇？

为了给改进后的[体系结构](concept-azure-machine-learning-architecture.md)让路，2018 年 9 月版本弃用并替换了 Azure Machine Learning Workbench 应用程序和其他一些早期功能。 为改善用户体验，此版本包含许多重大更新，这些更新由客户反馈促成。 从实验运行到模型部署的核心功能没有变化。 但现在，可以使用可靠的 <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> 和 [Azure CLI](reference-azure-machine-learning-cli.md) 来完成机器学习任务和管道。  

本文介绍了具体变化及其对使用 Azure Machine Learning Workbench 及其 API 构建的已有内容的影响。

## <a name="what-changed"></a>有何变化？

最新版 Azure 机器学习服务包括以下功能：
+ [简化的 Azure 资源模型](concept-azure-machine-learning-architecture.md)。
+ [全新门户 UI](how-to-track-experiments.md)，用于管理试验和计算目标。
+ 更全面的全新 Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>。
+ 已扩充的全新 [Azure CLI 扩展](reference-azure-machine-learning-cli.md)，用于机器学习。

重新设计的[架构](concept-azure-machine-learning-architecture.md)，易于使用。 无需使用多个 Azure 资源和帐户，只需使用 [Azure 机器学习服务工作区](concept-azure-machine-learning-architecture.md#workspace)即可。 可以在 [Azure 门户](quickstart-get-started.md)中快速创建工作区。 通过使用工作区，多个用户可以存储定型和部署计算目标、模型试验、Docker 映像、已部署模型等。

当前版本推出了改进后的全新 CLI 和 SDK 客户端，但桌面 workbench 应用程序本身已被弃用。 现在可以在 [Azure 门户的工作区仪表板](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal)中监视试验。 借助仪表板，可以获取试验历史记录、管理附加到工作区的计算目标、管理模型和 Docker 映像，甚至还能部署 Web 服务。

## <a name="how-do-i-migrate"></a>如何迁移？

在旧版 Azure 机器学习服务中创建的大多数项目，都存储在自己的本地存储或云存储中。 这些项目永远不会消失。 若要进行迁移，必须再次向更新后的 Azure 机器学习服务注册项目。 这篇[迁移文章](how-to-migrate.md)介绍了可以迁移哪些项目以及迁移方法。

<a name="timeline"></a>

## <a name="support-timeline"></a>支持时间线

2018 年 9 月之后，你仍然可以使用机器学习试验和模型管理帐户以及 Machine Learning Workbench 应用程序。 在此版本发布后的 3-4 个月内，将逐渐停止提供对以下资源的支持。 有关旧功能的文档，仍可访问目录底部的[“资源”部分](../desktop-workbench/tutorial-classifying-iris-part-1.md)。

|停用&nbsp;阶段|早期功能的支持详细信息|
|:---:|----------------|
|2018 年 12 月 4 日|通过 Azure 门户和 CLI 端创建 Azure 机器学习试验和模型管理帐户的功能支持将停止提供。 使用 CLI 创建机器学习计算环境的功能支持也已停止提供。 若有现有帐户，可以在此阶段继续使用 CLI 和桌面 Machine Learning Workbench。|
|2019 年 1 月 9 日|在此日期结束对其他全部内容的支持。 示例包括其余 API 和桌面 Machine Learning Workbench。|

立即[开始迁移](how-to-migrate.md)。 使用全新 <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>、[CLI](reference-azure-machine-learning-cli.md) 和[门户](quickstart-get-started.md)即可使用所有最新功能。

## <a name="what-about-run-histories"></a>运行历史记录又如何？

在一段时间内仍可继续访问运行历史记录。 当准备好迁移到更新版 Azure 机器学习服务时，若要保留副本，可导出这些运行历史记录。

在当前版本中，运行历史记录称为“试验”。 可使用 SDK、CLI 或 Azure 门户收集并探索模型的试验。

门户的工作区仪表板仅在 Microsoft Edge、Chrome 和 Firefox 浏览器上受支持：

[![联机门户](./media/overview-what-happened-to-workbench/image001.png)] (./media/overview-what-happened-to-workbench/image001.png#lightbox)


## <a name="can-i-still-prep-data"></a>我能否仍准备数据？

已有的数据准备文件不可移植到最新版本中，因为 Machine Learning Workbench 已遭弃用。 不过，仍可以准备数据用于建模。  

使用任何大小的数据集，可以在编写 Python 代码进行建模之前使用 [Azure 机器学习数据准备 SDK](http://aka.ms/data-prep-sdk) 快速准备数据。 

可以按照[本教程](tutorial-data-prep.md)，深入了解如何使用 Azure 机器学习数据准备 SDK。

## <a name="will-projects-persist"></a>项目是否继续存在？

不会丢失任何代码或工作。 在旧版本中，项目是包含本地目录的云实体。 在最新版本中，可使用本地配置文件将本地目录附加到 Azure 机器学习服务工作区。 请参阅[最新体系结构的关系图](concept-azure-machine-learning-architecture.md)。

大部分项目内容已存在于本地计算机上。 因此，只需在相应目录中创建配置文件，并在代码中引用它，即可连接到工作区。 了解如何[迁移现有项目](how-to-migrate.md#projects)。

了解如何开始[在 Python 中使用主 SDK](quickstart-create-workspace-with-python.md) 或使用 [Azure 门户](quickstart-get-started.md)。

## <a name="what-about-my-registered-models-and-images"></a>我注册的模型和映像又如何？
 
若要继续使用旧模型注册表中注册的模型，必须将它们迁移到新工作区。 若要迁移模型，请[下载模型并在新工作区中重新注册](how-to-migrate.md)。 

若要继续使用在旧映像注册表中创建的映像，必须在新工作区中重新创建它们。 可以按照[配置和创建映像](how-to-deploy-and-where.md#configureimage)部分重新创建这些映像。 

## <a name="what-about-deployed-web-services"></a>已部署 Web 服务又如何？

只要支持 Azure 容器服务，使用机器学习模型管理帐户部署为 Web 服务的模型就可以继续运行。 即使在机器学习模型管理帐户的支持结束后，这些 Web 服务也将生效。 不过，在旧 CLI 不再受支持后，管理这些 Web 服务的功能支持也不再提供。

在更高版本中，模型作为 Web 服务部署到 Azure 容器实例或 Azure Kubernetes 服务 (AKS) 群集中。 还可以部署到 FPGA 和 Azure IoT Edge。 有关详细信息，请参阅有关[部署方式及位置](how-to-deploy-and-where.md)的文章。 无需更改任何评分文件、依赖项和架构，即可使用全新 SDK 或 CLI 重新部署模型。 

## <a name="what-about-the-old-sdk-and-cli"></a>旧的 SDK 和 CLI 又如何？

它们将继续正常运行到 1 月。 请参阅前面的[时间线](#timeline)。 建议开始使用最新 SDK 或 CLI 新建试验和模型。

在最新版本中，使用全新 Python SDK，可以在任何 Python 环境中与 Azure 机器学习服务进行交互。 了解如何安装最新版 <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>。 还可以结合使用更新后的 [Azure 机器学习 CLI 扩展](reference-azure-machine-learning-cli.md)和丰富的 `az ml` 命令集，在任何命令行环境（包括 Azure Cloud Shell）中与服务进行交互。

## <a name="what-about-azure-machine-learning-for-visual-studio-code"></a>Azure Machine Learning for Visual Studio Code 又如何？

在此最新版本中，用于 Visual Studio Code 的 Azure 机器学习已进行了扩充和改进，可以支持上述新功能。

[![用于 Visual Studio Code 的 Azure 机器学习](./media/overview-what-happened-to-workbench/vscode.png)] (./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>域包又如何？

[计算机视觉、文本分析和预测](../desktop-workbench/reference-python-package-overview.md)的域包无法与最新版 Azure 机器学习结合使用。 不过，仍可以使用最新版 Azure 机器学习 Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>，生成并定型计算机视觉、文本分析和预测模型。 若要了解如何迁移使用计算机视觉、文本分析和预测包构建的已有模型，请联系 [AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com)。

## <a name="next-steps"></a>后续步骤

了解 [Azure 机器学习服务的最新体系结构](concept-azure-machine-learning-architecture.md)。 尝试快速入门或教程之一：

* [什么是 Azure 机器学习服务？](overview-what-is-azure-ml.md)
* [快速入门：使用 Python 创建工作区](quickstart-get-started.md)
* [教程：训练模型](tutorial-train-models-with-aml.md)
