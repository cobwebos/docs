---
title: 在 Azure 机器学习中管理和部署模型
description: 了解如何使用 Azure 机器学习来部署、管理和监视模型，以便持续改进它们。 可以在本地计算机上部署通过 Azure 机器学习训练的模型，也可从其他源进行部署。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: hjerez
ms.author: hjerez
ms.date: 09/24/2018
ms.openlocfilehash: d3e0b63d42ad8c6d4765f5120c26c5dfdf5ad6fb
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166531"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning"></a>通过 Azure 机器学习管理、部署和监视模型

本文介绍如何使用 Azure 机器学习来部署、管理和监视模型，以便持续改进它们。 可以在本地计算机上部署通过 Azure 机器学习训练的模型，也可从其他源进行部署。 

下图演示完整的部署工作流：[ ![Azure 机器学习的部署工作流](media/concept-model-management-and-deployment/deployment-pipeline.png) ](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

部署工作流包括以下步骤：
1. 在托管在 Azure 机器学习工作区的注册表中**注册模型**
1. **注册一个映像**，该映像在可移植容器中将一个模型与评分脚本和依赖项配对。 
1. 将映像作为 Web 服务**部署**到云或边缘设备
1. **监视和收集数据**

每个步骤都可以单独执行，或者作为单个部署命令的一部分执行。 另外还可以将部署集成到 **CI/CD 工作流**中，如下图所示。

[ ![“Azure 机器学习持续集成/持续部署 (CI/CD) 循环”](media/concept-model-management-and-deployment/model-ci-cd.png) ](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)


## <a name="step-1-register-model"></a>步骤 1：注册模型

模型注册表跟踪 Azure 机器学习工作区中的所有模型。
模型按名称和版本标识。 每次使用与现有名称相同的名称来注册模型时，注册表都会将版本递增。 也可在注册过程中提供其他的元数据标记，这些标记可以在搜索模型时使用。

映像正在使用的模型无法删除。

## <a name="step-2-register-image"></a>步骤 2：注册映像

可以通过映像以及使用模型所需的所有组件进行可靠的模型部署。 映像包含以下项：

* 模型
* 评分引擎
* 评分文件或应用程序
* 对模型评分所需的任何依赖项

映像也可包含用于日志记录和监视的 SDK 组件。 SDK 日志数据可以用来调整或重新训练模型，其中包含模型的输入和输出。

Azure 机器学习支持最常用的框架，但通常情况下，任何可以进行 pip 安装的框架均可行。

创建工作区时，也会创建多个其他的供该工作区使用的 Azure 资源。
用于创建映像的所有对象都存储在工作区的 Azure 存储帐户中。 映像在创建后存储在 Azure 容器注册表中。 可以在创建映像时提供其他的元数据标记，这些标记也由映像注册表存储，可以通过查询这些标记来查找映像。

## <a name="step-3-deploy-image"></a>步骤 3：部署映像

可以将注册的映像部署到云或边缘设备。 部署过程创建对模型进行监视、负载均衡和自动缩放操作所需的所有资源。 可以通过基于证书的身份验证来确保对部署的服务进行安全的访问，只需在部署过程中提供安全资产即可。 也可升级现有的部署，以便使用更新的映像。

Web 服务部署也可进行搜索。 例如，可以搜索特定模型或映像的所有部署。

[ ![推断目标](media/concept-model-management-and-deployment/inferencing-targets.png) ](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

可以将映像部署到云中的下述[部署目标](how-to-deploy-and-where.md)：

* Azure 容器实例
* Azure Kubernetes 服务
* Azure FPGA 计算机
* Azure IoT Edge 设备

部署服务时，推断请求会自动进行负载均衡，群集会根据高峰需求进行缩放。 [有关服务的遥测数据](https://docs.microsoft.com/python/api/azureml-telemetry/azureml.telemetry?view=azure-ml-py)可以捕获到与工作区关联的 Azure Application Insights 服务中。

## <a name="step-4-monitor-models-and-collect-data"></a>步骤 4：监视模型和收集数据

可以使用适用于模型日志记录和数据捕获的 SDK 来监视模型的输入、输出和其他相关数据。 数据以 Blob 形式存储在工作区的 Azure 存储帐户中。

若要将 SDK 与模型配合使用，请将 SDK 导入评分脚本或应用程序中。 然后，可以使用 SDK 来记录数据，例如参数、结果或输入详细信息。

如果决定在每次部署映像时都[启用模型数据收集](how-to-enable-data-collection.md)，系统会自动预配捕获数据所需的详细信息，例如个人 Blob 存储的凭据。

> [!Important]
> Microsoft 看不到你从模型中收集的数据。 数据直接发送到工作区的 Azure 存储帐户。

## <a name="next-steps"></a>后续步骤

详细了解在使用 Azure 机器学习服务时，[部署模型的方式和位置](how-to-deploy-and-where.md)。
