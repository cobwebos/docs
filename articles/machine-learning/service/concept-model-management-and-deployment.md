---
title: 管理、注册、部署和监视 ML 模型
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务来部署、管理和监视模型，以便不断改进它们。 可以在本地计算机上或从其他源部署使用 Azure 机器学习服务训练的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 1/23/2019
ms.custom: seodec18
ms.openlocfilehash: 2cd2d328d33744854bc525e5ecf1dfa3b6e4bcc8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275430"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>使用 Azure 机器学习服务管理、部署和监视模型

本文介绍如何使用 Azure 机器学习服务来部署、管理和监视模型，以便不断改进它们。 可以在本地计算机上部署通过 Azure 机器学习训练的模型，也可从其他源进行部署。 

下图演示了完整的部署工作流：[![为 Azure 机器学习部署工作流](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

部署工作流包括以下步骤：
1. 在 Azure 机器学习服务工作区中托管的注册表中**注册模型**
1. **注册一个映像**，该映像在可移植容器中将一个模型与评分脚本和依赖项配对。 
1. 将映像作为 Web 服务**部署**到云或边缘设备
1. **监视和收集数据**
1. 更新部署以使用新映像。

每个步骤都可以单独执行，或者作为单个部署命令的一部分执行。 另外还可以将部署集成到 **CI/CD 工作流**中，如下图所示。

[!['Azure 机器学习持续集成/持续部署 (CI/CD) 周期 '](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

## <a name="step-1-register-model"></a>步骤 1：注册模型

通过模型注册，可在 Azure 云的工作区中存储模型并确定模型版本。 使用模型注册表，可轻松组织和跟踪定型的模型。
 
按名称和版本标识已注册的模型。 每次使用与现有名称相同的名称来注册模型时，注册表都会将版本递增。 也可在注册过程中提供其他的元数据标记，这些标记可以在搜索模型时使用。 Azure 机器学习服务支持可以使用 Python 3 进行加载的任何模型。 

映像正在使用的模型无法删除。

有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md#registermodel)的注册模型部分。

有关如何注册采用 pickle 格式存储的模型示例，请参阅[教程：训练映像分类模型](tutorial-deploy-models-with-aml.md)。

要了解如何使用 ONNX 模型，请参阅 [ONNX 和 Azure 机器学习](how-to-build-deploy-onnx.md)文档。

## <a name="step-2-register-image"></a>步骤 2：注册映像

可以通过映像以及使用模型所需的所有组件进行可靠的模型部署。 映像包含以下项：

* 模型
* 评分引擎
* 评分文件或应用程序
* 对模型评分所需的任何依赖项

映像也可包含用于日志记录和监视的 SDK 组件。 SDK 日志数据可以用来调整或重新训练模型，其中包含模型的输入和输出。

Azure 机器学习支持最常用的框架，但通常情况下，任何可以进行 pip 安装的框架均可行。

创建工作区时，也会创建多个其他的供该工作区使用的 Azure 资源。
用于创建的默认图像的所有对象都存储在工作区中的 Azure 存储帐户。 创建映像时，可以提供其他元数据标记。 元数据标记还由映像注册表存储，并且可以进行查询以查找映像。

此外可以使用自定义映像，可以上传到 Azure 容器注册表和 Azure 机器学习服务使用。

有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md#configureimage)的配置和注册映像部分。

## <a name="step-3-deploy-image"></a>步骤 3：部署映像

可以将注册的映像部署到云或边缘设备。 部署过程创建对模型进行监视、负载均衡和自动缩放操作所需的所有资源。 可以通过基于证书的身份验证来确保对部署的服务进行安全的访问，只需在部署过程中提供安全资产即可。 也可升级现有的部署，以便使用更新的映像。

Web 服务部署也可进行搜索。 例如，可以搜索特定模型或映像的所有部署。

[![Inferencing 目标](media/concept-model-management-and-deployment/inferencing-targets.png)](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

可以将映像部署到云中的下述部署目标：

* Azure 容器实例
* Azure Kubernetes 服务
* Azure FPGA 计算机
* Azure IoT Edge 设备

部署服务时，推断请求会自动进行负载均衡，群集会根据高峰需求进行缩放。 [有关服务的遥测数据可以捕获](how-to-enable-app-insights.md)到与工作区关联的 Azure Application Insights 服务中。

有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md#deploy)的部署部分。

## <a name="step-4-monitor-models-and-collect-data"></a>步骤 4：监视模型和收集数据

可以使用适用于模型日志记录和数据捕获的 SDK 来监视模型的输入、输出和其他相关数据。 数据以 Blob 形式存储在工作区的 Azure 存储帐户中。

若要将 SDK 与模型配合使用，请将 SDK 导入评分脚本或应用程序中。 然后，可以使用 SDK 来记录数据，例如参数、结果或输入详细信息。

如果决定在每次部署映像时都启用模型数据收集，系统会自动预配捕获数据所需的详细信息，例如个人 Blob 存储的凭据。

> [!Important]
> Microsoft 看不到你从模型中收集的数据。 数据直接发送到工作区的 Azure 存储帐户。

有关详细信息，请参阅[如何启用模型数据收集](how-to-enable-data-collection.md)。

## <a name="step-5-update-the-deployment"></a>步骤 5：更新部署

系统不自动注册模型的更新。 同样，注册新映像不会自动更新从先前版本的映像创建的部署。 相反，必须手动注册模型，注册映像，然后更新模型。 有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md#update)的更新部分。

## <a name="next-steps"></a>后续步骤

详细了解在使用 Azure 机器学习服务时，[部署模型的方式和位置](how-to-deploy-and-where.md)。 有关部署的示例，请参阅[教程：部署 Azure 容器实例中的图像分类模型](tutorial-deploy-models-with-aml.md)。

了解如何创建[使用部署为 Web 服务的模型](how-to-consume-web-service.md)的客户端应用程序和服务。
