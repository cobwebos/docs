---
title: 什么是 FPGA？ – Project Brainwave – Azure 机器学习
description: 了解如何借助 FPGA 加快模型和深度神经网络的速度。
services: machine-learning
ms.service: machine-learning
ms.component: service
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/31/2018
ms.openlocfilehash: 5ed94c3b750c927ec48959c12388bd22de3d3df4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261944"
---
# <a name="what-is-fpga-and-project-brainwave"></a>什么是 FPGA 和 Project Brainwave？

本文简单介绍了现场可编程门阵列 (FPGA)，以及如何将 FPGA 与 Azure 机器学习集成，以便提供实时 AI。

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA 与CPU、GPU 和 ASIC 的对比

FPGA 包含一组可编程的逻辑块以及由可反复配置的互连组成的层次结构，因此可以在生产之后以不同方式来配置块。

与其他芯片相比，FPGA 既有可编程性，又有很好的性能：

![Azure 机器学习 FPGA 比较](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

- **中央处理单元 (CPU)** 是常规用途的处理器。 CPU 性能不适合图形和视频处理。
- **图形处理单元 (GPU)** 提供并行处理，是 AI 计算的常用选择。 使用 GPU 进行并行处理获得的图像渲染速度快于 CPU。
- **专用集成电路 (ASIC)**（例如 Google 的 TensorFlow 处理器单元）是自定义的电路。 虽然此类芯片效率最高，但 ASIC 不灵活。
- **FPGA**（例如 Azure 上提供的）提供的性能接近 ASIC，但很灵活，可以在以后反复配置。

现在，每个新的 Azure 服务器中都有 FPGA。 Microsoft 已经将 FPGA 用于必应搜索排名、深层神经网络 (DNN) 评估以及软件定义网络 (SDN) 加速。 这些 FPGA 实现在降低延迟的同时，也为其他任务空出了 CPU。

## <a name="project-brainwave-on-azure"></a>Azure 上的 Project Brainwave

Project Brainwave 是一种硬件体系结构，其设计以 Intel 的 FPGA 设备为基础，用于加速实时 AI 计算。 有了这种支持 FPGA 的经济实用的体系结构，就可以尽快运行受训的神经网络，并且可以降低延迟。 Project Brainwave 可以在 FPGA 之间并行化预先训练的 DNN 来横向扩展服务。

- 性能

    可以通过 FPGA 确保实时推断请求的低延迟。 批处理是指将请求拆分成较小的片段，然后将这些片段馈送给处理器以改进硬件利用率。 批处理效率不高，可能导致延迟。 Brainwave 不需批处理，因此与 CPU 和 GPU 相比，其延迟降低 10 倍。

- 灵活性

    FPGA 可以针对不同类型的机器学习模型反复配置。 利用这种灵活性，可以更轻松地根据最优化的数值精度和所用内存模型来加速应用程序。

    新的机器学习技术在不断地开发，而 Project Brainwave 的硬件设计也在快速地发展。 由于 FPGA 可以反复配置，因此可以跟上快速变化的 AI 算法的要求。

- 缩放

    Microsoft Azure 是世界上在 FPGA 中的最大云投资。 可以在 Azure 的缩放基础结构上运行 Brainwave。

现在已发布与 Azure 机器学习集成的 Project Brainwave 预览版。 此外还提供一个受限预览版，可以在边缘设备上使用 Project Brainwave，这样你就可以在自己的企业和设施中充分利用其计算速度。

在当前预览版中，Brainwave 仅限于 TensorFlow 部署和基于 ResNet50 的神经网络，可以在 Intel FPGA 硬件上用于图像分类和识别。 计划支持更多的库模型和其他框架。

以下方案在 Project Brainwave 体系结构上使用 FPGA：

- 自动化光学检查系统。 请参阅 [Real-time AI: Microsoft announces preview of Project Brainwave](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)（实时 AI：Microsoft 宣布推出 Project Brainwave 预览版）。
- 地表覆盖制图。 请参阅 [How to Use FPGAs for Deep Learning Inference to Perform Land Cover Mapping on Terabytes of Aerial Images](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)（如何将 FPGA 用于深度学习推理，以便针对 TB 级的航拍图像进行地表覆盖制图）。

## <a name="how-to-deploy-a-web-service-to-an-fpga"></a>如何将 Web 服务部署到 FPGA？

使用 ResNet50 作为特征化器在 Azure 中创建图像识别服务的大致流程如下：

1. ResNet50 已部署到 Brainwave 中。 可以先使用 TensorFlow 生成其他图（日期输入、分类等），然后使用服务定义 json 文件定义一个管道（输入 -> 特征化 -> 分类）。 将定义和图压缩到 zip 文件中，然后将 zip 文件上传到 Azure Blob 存储。
2. 使用 Azure ML 模型管理 API 注册该模型，将 zip 文件置于 Blob 存储中。
3. 使用 Azure ML 模型管理 API 通过注册的模型部署服务。

有关此过程的详细信息，请参阅[使用 Azure 机器学习将模型部署为 FPGA 上的 Web 服务](how-to-deploy-fpga-web-service.md)一文。


## <a name="start-using-fpga"></a>FPGA 入门

Azure 机器学习硬件加速模型允许向 Azure 云中的 FPGA 部署已训练的 DNN 模型。 如要入门，请参阅：

- [将模型部署为 FPGA 上的 Web 服务](how-to-deploy-fpga-web-service.md)
- [Microsoft Azure Machine Learning Hardware Accelerated Models Powered by Project Brainwave](https://github.com/azure/aml-real-time-ai)（由 Project Brainwave 提供支持的 Microsoft Azure 机器学习硬件加速模型）。

## <a name="next-steps"></a>后续步骤

[将模型部署为 FPGA 上的 Web 服务](how-to-deploy-fpga-web-service.md)

[了解如何安装 FPGA SDK](reference-fpga-package-overview.md)

[Hyperscale hardware: ML at scale on top of Azure + FPGA : Build 2018 (video)](https://www.youtube.com/watch?v=BMgQAHIx2eY)（超大规模硬件：基于 Azure + FPGA：内部版本 2018 的大规模机器学习（视频））

[Inside the Microsoft FPGA-based configurable cloud (video)](https://channel9.msdn.com/Events/Build/2017/B8063)（深入了解基于 Microsoft FPGA 的可配置云（视频））

[Microsoft unveils Project Brainwave for real-time AI](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/)（Microsoft 揭示用于实时 AI 的 Project Brainwave）