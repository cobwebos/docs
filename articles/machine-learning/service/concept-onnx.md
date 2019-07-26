---
title: ONNX 的高性能、跨平台推理
titleSuffix: Azure Machine Learning service
description: 了解 ONNX 和用于加速模型的 ONNX 运行时
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: 10c47c5dbae0ffb204a78ff510e457cce9a25ff0
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361103"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX 和 Azure 机器学习：创建和加速 ML 模型

了解如何使用[开放式神经网络交换](https://onnx.ai)(ONNX) 来帮助优化机器学习模型的推理。 推理或模型计分是部署模型用于预测的阶段, 最常见的是生产数据。 

优化用于推理 (或模型评分) 的机器学习模型非常困难, 因为您需要优化模型和推理库, 以充分利用硬件功能。 如果要在不同类型的平台 (云/边缘、CPU/GPU 等) 上获得最佳性能, 则问题会变得非常困难, 因为每个平台都有不同的功能和特性。 如果模型来自需要在各种平台上运行的各种框架, 则复杂性会提高。 优化框架和硬件的所有不同组合非常耗时。 一种解决方案是在首选框架中训练一次, 并在云或边缘上的任意位置运行。 这就是 ONNX 的位置。

Microsoft 和合作伙伴社区创建 ONNX 作为表示机器学习模型的开放标准。 [许多框架](https://onnx.ai/supported-tools)(包括 TensorFlow、PyTorch、Scikit-learn、Keras、Chainer、MXNET 和 MATLAB) 中的模型都可以导出或转换为标准 ONNX 格式。 在模型采用 ONNX 格式后, 即可在各种平台和设备上运行。

[ONNX 运行时](https://github.com/Microsoft/onnxruntime)是用于将 ONNX 模型部署到生产环境的高性能推理引擎。 它针对云和边缘进行了优化, 适用于 Linux、Windows 和 Mac。 编写C++, 它还具有 C、Python 和C# api。 ONNX 运行时为所有 ONNX 规范提供支持, 并与不同硬件 (如 TensorRT 上的 NVidia Gpu) 上的加速器集成。

ONNX 运行时用于高规模 Microsoft 服务, 如必应、Office 和认知服务。 性能提升取决于许多因素, 但这些 Microsoft 服务已看到__CPU 平均性能__大幅提高。 ONNX 运行时也用作数百个设备上 Windows ML 的一部分。 您可以将运行时与 Azure 机器学习 services 一起使用。 通过使用 ONNX 运行时, 您可以从广泛的生产级优化、测试和不断改进中受益。

[![显示定型、转换器和部署的 ONNX 流程图](media/concept-onnx/onnx.png)](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>获取 ONNX 模型

可通过以下几种方式获取 ONNX 模型：
+ 在 Azure 机器学习服务中训练新的 ONNX 模型 (请参阅本文底部的示例)
+ 将其他格式的现有模型转换为 ONNX (请参阅[教程](https://github.com/onnx/tutorials)) 
+ 从[ONNX 模型 Zoo](https://github.com/onnx/models)获取预先训练的 ONNX 模型 (请参阅本文底部的示例)
+ 从 [Azure 自定义影像服务](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)生成自定义 ONNX 模型 

许多模型 (包括图像分类、对象检测和文本处理) 都可以表示为 ONNX 模型。 但某些模型可能无法成功转换。 如果遇到这种情况, 请在使用的相应转换器的 GitHub 中指出问题。 您可以继续使用现有的格式模型, 直到问题得到解决。

## <a name="deploy-onnx-models-in-azure"></a>在 Azure 中部署 ONNX 模型

使用 Azure 机器学习服务，可以部署、管理和监视 ONNX 模型。 使用标准[部署工作流](concept-model-management-and-deployment.md)和 ONNX 运行时，可以创建在云中托管的 REST 终结点。 请参阅本文末尾的 Jupyter 笔记本示例, 亲自尝试一下。 

### <a name="install-and-use-onnx-runtime-with-python"></a>使用 Python 安装和使用 ONNX 运行时

[PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime)、 [GPU](https://pypi.org/project/onnxruntime-gpu)) 上提供了适用于 ONNX 运行时的 Python 包。 请先阅读[系统要求](https://github.com/Microsoft/onnxruntime#system-requirements), 然后再安装。 

 若要安装适用于 Python 的 ONNX 运行时, 请使用以下命令之一: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

若要在 Python 脚本中调用 ONNX 运行时，请使用：    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

模型附带的文档通常会指示有关使用模型的输入和输出。 还可使用可视化工具（如 [Netron](https://github.com/lutzroeder/Netron)）查看模型。 ONNX 运行时还可以查询模型元数据、输入和输出：    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

若要推理模型，请使用 `run`，并传入要返回的输出列表（如果需要所有输出，则保留为空）和输入值的映射。 结果是输出列表。  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

有关完整的 Python API 参考，请参阅 [ONNX 运行时参考文档](https://aka.ms/onnxruntime-python)。    

## <a name="examples"></a>示例

有关创建和部署 ONNX 模型的示例笔记本，请参阅 [how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx)。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>详细信息

详细了解 ONNX 或为项目做出贡献：
+ [ONNX 项目网站](https://onnx.ai)
+ [GitHub 上的 ONNX 代码](https://github.com/onnx/onnx)

详细了解 ONNX 运行时或者为项目做出贡献：
+ [ONNX 运行时 GitHub 存储库](https://github.com/Microsoft/onnxruntime)


