---
title: MLOps:管理、 部署和监视机器学习模型
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务的 MLOps： 部署、 管理和监视才能持续改进这些模型。 可以在本地计算机上或从其他源部署使用 Azure 机器学习服务训练的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 11a4a17d7816d2302b6549cffb9517e10ad1258d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442350"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps:使用 Azure 机器学习服务管理、部署和监视模型

在本文中，了解如何使用 Azure 机器学习服务来管理您的模型的生命周期。 Azure 机器学习使用机器学习操作 (MLOps) 的方法，这将提高质量和机器学习解决方案的一致性。 

Azure 机器学习服务提供了以下 MLOps 功能：

- **部署从任何位置的机器学习项目**
- **监视适用于机器学习应用程序操作和机器学习相关的问题**-比较模型输入之间培训和推理、 浏览特定于模型的指标和机器学习基础结构上提供监视和警报。
- **捕获建立机器学习生命周期的端到端审核跟踪所需的数据**，包括谁正在发布模型、 为什么要进行更改，以及模型已部署或在生产中使用时。
- **自动执行 Azure 机器学习和 Azure DevOps 的端到端机器学习生命周期**频繁更新模型，测试新的模型，并持续推出新的机器学习模型以及其他应用程序和服务。

再听些上背后的概念 MLOps 以及如何将 Azure 机器学习服务的应用，请观看以下视频。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="deploy-ml-projects-from-anywhere"></a>部署从任何位置的机器学习项目

### <a name="turn-your-training-process-into-a-reproducible-pipeline"></a>将训练过程变成可重现的管道
使用 Azure 机器学习中的机器学习管道来整合所有在模型定型过程中，从准备数据到功能提取到的超参数优化模型评估所涉及的步骤。

有关详细信息，请参阅[ML 管道](concept-ml-pipelines.md)。

### <a name="register-and-track-ml-models"></a>注册并跟踪机器学习模型

通过模型注册，可在 Azure 云的工作区中存储模型并确定模型版本。 使用模型注册表，可轻松组织和跟踪定型的模型。

> [!TIP]
> 已注册的模型是构成模型的一个或多个文件的逻辑容器。 例如，如果有多个文件中存储的模型，您可以将它们注册为单一模型在 Azure 机器学习工作区中。 注册后，你可以然后下载或部署已注册的模型并接收已注册的所有文件。
 
按名称和版本标识已注册的模型。 每次使用与现有名称相同的名称来注册模型时，注册表都会将版本递增。 也可在注册过程中提供其他的元数据标记，这些标记可以在搜索模型时使用。 Azure 机器学习服务支持使用 Python 3.5.2 加载或更高版本可以是任何模型。

> [!TIP]
> 您还可以注册 Azure 机器学习服务外部训练的模型。

无法删除已注册的模型正在使用的活动部署中。
有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md#registermodel)的注册模型部分。

### <a name="package-and-debug-models"></a>包和调试模型

然后再到生产环境中部署模型，它被打包到 Docker 映像。 在大多数情况下，映像创建自动在后台发生在部署过程。 对于高级方案，可以手动指定图像。

如果您运行部署问题，你可以部署进行故障排除和调试在本地开发环境。

有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md#registermodel)并[部署故障排除](how-to-troubleshoot-deployment.md)。

### <a name="validate-and-profile-models"></a>验证和分析模型

Azure 机器学习服务可以使用分析来确定部署您的模型时要使用的理想 CPU 和内存的设置。 模型验证一切都按照此过程中，使用你提供的数据事件探查过程的一部分。

### <a name="convert-and-optimize-models"></a>转换和优化模型

将转换为模型[开放神经网络交换](https://onnx.ai)(ONNX) 可能会提高性能。 一般情况下，将转换为 ONNX 可以产生 2 倍的性能提升。

使用 Azure 机器学习服务 ONNX 详细信息，请参阅[创建，并加速机器学习模型](concept-onnx.md)文章。

### <a name="use-models"></a>使用模型

定型的机器学习模型可部署为 web 服务在云中或本地开发环境。 此外可以将模型部署到 Azure IoT Edge 设备。 部署可以使用 CPU、 GPU 或现场可编程门阵列 (FPGA) 的推断。 此外可以使用 Power BI 中的模型。

如果使用的模型作为 web 服务或 IoT Edge 设备，你将提供以下各项：

* 用于评分提交到服务/设备的数据模型。
* 入口脚本。 此脚本接受请求，使用模型用于评分的数据，并返回响应。
* 描述模型和入口脚本所需的依赖项的 conda 环境文件。
* 所需模型和输入脚本的文本、 数据等任何其他资产。

这些资产打包到 Docker 映像，并部署为 web 服务或 IoT Edge 模块。

（可选） 可以使用以下参数来进一步优化部署：

* 启用 GPU:用于启用 GPU 支持 Docker 映像中。 必须在 Microsoft Azure 服务，例如，Azure 容器实例、 Azure Kubernetes 服务、 Azure 机器学习计算或 Azure 虚拟机上使用该映像。
* 额外的 docker 文件的步骤：包含其他 Docker 步骤，创建 Docker 映像时要运行的文件。
* 基本映像：自定义映像以用作基本映像。 如果不使用自定义映像，由 Azure 机器学习服务提供的基本映像。

你还提供目标部署平台的配置。 例如，VM 系列类型、 可用内存和部署到 Azure Kubernetes 服务时的核心数。

创建映像时，也会添加所需的 Azure 机器学习服务组件。 例如，运行 web 服务并与 IoT Edge 进行交互所需的资产。

> [!NOTE]
> 无法修改或更改 web 服务器或使用 Docker 映像中的 IoT Edge 组件。 Azure 机器学习服务使用的 web 服务器配置和测试并受 Microsoft 的 IoT Edge 组件。

#### <a name="web-service"></a>Web 服务

可以使用您的模型中**web 服务**以下的计算目标：

* Azure 容器实例
* Azure Kubernetes 服务
* 本地开发环境

若要将模型作为 web 服务部署，必须提供以下各项：

* 模型或模型的系综。
* 若要使用该模型所需的依赖项。 例如，一个脚本来接受请求并调用模型、 conda 依赖项等。
* 部署配置，以描述如何以及在何处部署模型。

有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md)。

#### <a name="iot-edge-devices"></a>IoT Edge 设备

可以将模型与 IoT 设备，通过使用**Azure IoT Edge 模块**。 IoT Edge 模块部署到启用推理或模型评分，在设备上的硬件设备。

有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md)。

### <a name="analytics"></a>分析

Microsoft Power BI 支持使用机器学习模型进行数据分析。 有关详细信息，请参阅[Power BI （预览版） 中的 Azure 机器学习集成](https://docs.microsoft.com/power-bi/service-machine-learning-integration)。


## <a name="monitor-ml-applications-for-operational-and-ml-related-issues"></a>监视适用于机器学习应用程序操作和机器学习相关的问题

监视可以了解哪些数据发送到您的模型，并且它将返回的预测。

此信息可帮助您了解您的模型的使用方式。 收集的输入的数据也可能会很有用训练的模型的未来版本中。

有关详细信息，请参阅[如何启用模型数据收集](how-to-enable-data-collection.md)。


## <a name="capture-an-end-to-end-audit-trail-of-the-ml-lifecycle"></a>捕获的机器学习生命周期的端到端审核线索

Azure 机器学习使你能够跟踪所有机器学习资产的端到端审核记录。 具体而言：

- Azure 机器学习与 Git 跟踪的信息的存储库 / 分支 / 提交你的代码来源。
- Azure 机器学习数据集帮助您跟踪和版本的数据。
- Azure 机器学习运行历史记录管理代码、 数据和计算用于训练模型。
- Azure 机器学习模型注册表会捕获所有与您的模型 （哪个实验训练它，它正在部署的位置，如果处于正常状态的及其部署） 相关联的元数据。

## <a name="automate-the-end-to-end-ml-lifecycle"></a>自动执行端到端机器学习生命周期 

可以使用 GitHub 和 Azure 管道创建训练模型的持续集成过程。 在典型方案中，数据科研人员签入项目中，Git 存储库的更改时 Azure 管道将开始训练运行。 然后可以检查运行的结果以查看已训练模型的性能特征。 此外可以创建的管道，会将模型作为 web 服务部署。

[Azure 机器学习扩展](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml)可以更轻松地使用 Azure 管道。 到 Azure 管道，它提供以下增强功能：

* 定义服务连接时，请启用工作区中选择。
* 启用发布由在训练管道中创建的已训练模型触发的管道。

在 Azure 机器学习中使用 Azure 管道的详细信息，请参阅[持续集成和部署机器学习模型与 Azure 管道](/azure/devops/pipelines/targets/azure-machine-learning)一文和[Azure 机器学习服务 MLOps](https://aka.ms/mlops)存储库。

## <a name="next-steps"></a>后续步骤

详细了解在使用 Azure 机器学习服务时，[部署模型的方式和位置](how-to-deploy-and-where.md)。 有关部署的示例，请参阅[教程：部署 Azure 容器实例中的图像分类模型](tutorial-deploy-models-with-aml.md)。

了解如何创建[持续集成和部署机器学习模型与 Azure 管道](/azure/devops/pipelines/targets/azure-machine-learning)。 

了解如何创建[使用部署为 Web 服务的模型](how-to-consume-web-service.md)的客户端应用程序和服务。
