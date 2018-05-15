---
title: 适用于 Azure 机器学习的硬件加速 FPGA 包
description: 了解适用于 Azure 机器学习用户的 Python 包。
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: routlaw
author: rloutlaw
ms.date: 05/07/2018
ms.openlocfilehash: 905f6943470acfd3051a33db6f6f3269470406d7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="azure-machine-learning-hardware-acceleration-package"></a>Azure 机器学习硬件加速包

Azure 机器学习硬件加速包是适用于 Azure 机器学习的 Python pip 可安装扩展，方便数据科学家和 AI 开发人员快速执行以下操作：

+ 使用量化版 ResNet 50 抽取图像的特征

+ 根据这些特征将分类器定型

+ 将模型部署到 Azure 的[现场可编程门阵列 (FPGA)](concept-accelerate-with-fpgas.md)，以便进行超低延迟推断

## <a name="prerequisites"></a>先决条件

1. 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

1. 必须创建 Azure 机器学习模型管理帐户。 有关如何创建帐户的详细信息，请参阅 [Azure 机器学习快速入门和 Workbench 安装](../service/quickstart-installation.md)文档。 

1. 必须安装此包。 

 
## <a name="how-to-install-the-package"></a>如何安装包

1. 下载并安装最新版本的 [Git](https://git-scm.com/downloads)。

2. 安装 [Anaconda (Python 3.6)](https://conda.io/miniconda.html)

3. 若要下载预配置的 Anaconda 环境，请使用 Git 提示中的下述命令：

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```
5. 若要创建环境，请打开“Anaconda 提示”窗口并使用以下命令：

    ```
    conda env create -f aml-real-time-ai/environment.yml
    ```

6. 使用以下命令激活该环境：

    ```
    conda activate amlrealtimeai
    ```

## <a name="sample-code"></a>代码示例

此示例代码详细演示了如何使用 SDK 将模型部署到 FPGA。

1. 导入包：
   ```python
   import amlrealtimeai
   from amlrealtimeai import resnet50
   ```

1. 预处理图像：
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. 抽取图像特征：
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. 创建分类器：
   ```python
   model.import_graph_def(include_featurizer=False)
   print(model.classifier_input)
   print(model.classifier_output)
   ```

1. 创建服务定义：
   ```python
   from amlrealtimeai.pipeline import ServiceDefinition, TensorflowStage, BrainWaveStage
   save_path = os.path.expanduser('~/models/save')
   service_def_path = os.path.join(save_path, 'service_def.zip')

   service_def = ServiceDefinition()
   service_def.pipeline.append(TensorflowStage(tf.Session(), in_images, image_tensors))
   service_def.pipeline.append(BrainWaveStage(model))
   service_def.pipeline.append(TensorflowStage(tf.Session(), model.classifier_input, model.classifier_output))
   service_def.save(service_def_path)
   print(service_def_path)
   ```
 
1. 准备要在 FPGA 上运行的模型：
   ```python
   from amlrealtimeai import DeploymentClient

   subscription_id = "<Your Azure Subscription ID>"
   resource_group = "<Your Azure Resource Group Name>"
   model_management_account = "<Your AzureML Model Management Account Name>"

   model_name = "resnet50-model"
   service_name = "quickstart-service"

   deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account)
   ```

1. 部署要在 FPGA 上运行的模型：
   ```python
   service = deployment_client.get_service_by_name(service_name)
   model_id = deployment_client.register_model(model_name, service_def_path)

   if(service is None):
      service = deployment_client.create_service(service_name, model_id)    
   else:
      service = deployment_client.update_service(service.id, model_id)
   ```

1. 创建客户端：
    ```python
   from amlrealtimeai import PredictionClient
   client = PredictionClient(service.ipAddress, service.port)  
   ```

1. 调用 API：
   ```python
   image_file = R'C:\path_to_file\image.jpg'
   results = client.score_image(image_file)
   ```

## <a name="reporting-issues"></a>报告问题

请通过[论坛](https://aka.ms/aml-forum)报告在使用包时遇到的问题。

## <a name="next-steps"></a>后续步骤

[将模型部署为 FPGA 上的 Web 服务](how-to-deploy-fpga-web-service.md)