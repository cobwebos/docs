---
title: 什么是 FPGA 和 Project Brainwave？ - Azure 机器学习
description: 了解如何在 Azure 上借助 FPGA 加快模型和深度神经网络的速度。 本文简单介绍了现场可编程门阵列 (FPGA)，以及在将模型部署到 Azure FPGA 时，如何通过 Azure 机器学习来提供实时人工智能 (AI)。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 9/24/2018
ms.openlocfilehash: adcd812bc63f98e4cbde949946bf32a037c6d704
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2018
ms.locfileid: "47158678"
---
# <a name="what-is-fpga-and-project-brainwave"></a>什么是 FPGA 和 Project Brainwave？

本文简单介绍了现场可编程门阵列 (FPGA)，以及在将模型部署到 Azure FPGA 时，如何通过 Azure 机器学习来提供实时人工智能 (AI)。

FPGA 包含一组可编程的逻辑块以及由可反复配置的互连组成的层次结构。 这种互连可以在生产之后以不同方式来配置块。 与其他芯片相比，FPGA 既有可编程性，又有很好的性能。

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA 与CPU、GPU 和 ASIC 的对比

![Azure 机器学习 FPGA 比较](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|处理器||Description|
|---|:-------:|------|
|特定于应用程序的集成电路|ASIC|自定义电路（例如 Google 的 TensorFlow 处理器单元 (TPU)）的效率最高。 它们不能根据需求的变化重新配置。|
|现场可编程门阵列|FPGA|FPGA（例如 Azure 上提供的）提供的性能接近 ASIC，但很灵活，可以在以后反复配置以实现新的逻辑。|
|图形处理单元|GPU|进行 AI 计算时的常用选择，提供并行处理功能，在进行图形渲染时速度快于 CPU。|
|中央处理单元|CPU|其性能不适合图形和视频处理的常规用途处理器。|

## <a name="project-brainwave-on-azure"></a>Azure 上的 Project Brainwave

[Project Brainwave](https://www.microsoft.com/research/project/project-brainwave/) 是 Microsoft 推出的经济实用的硬件体系结构，以 Intel 的 FPGA 设备为基础，可供数据科学家和开发人员用于加速实时 AI 计算。  这种支持 FPGA 的体系结构提供**性能**、**灵活性**和**可伸缩性**，可以在 Azure 上使用。

**可以通过 FPGA 确保实时推断请求的低延迟。** 批处理是指收集大量的数据，然后将这些数据馈送给处理器以改进硬件利用率。 批处理可能导致延迟，因为需要处理更多的数据，但可以提高吞吐量。 Brainwave 在实现神经处理单元时不需批处理，因此与 CPU 和 GPU 相比，其延迟降低很多倍。

### <a name="reconfigurable-power"></a>可反复配置的功能
**FPGA 可以针对不同类型的机器学习模型反复配置。** 利用这种灵活性，可以更轻松地根据最优化的数值精度和所用内存模型来加速应用程序。

新的机器学习技术在不断地开发，而 Project Brainwave 的硬件设计也在快速地发展。 由于 FPGA 可以反复配置，因此可以跟上快速变化的 AI 算法的要求。

### <a name="whats-supported-on-azure"></a>Azure 支持的操作
**Microsoft Azure 是世界上在 FPGA 中的最大云投资。** 可以在 Azure 的缩放基础结构上运行 Project Brainwave。

目前，Project Brainwave 支持以下功能：
+ 图像分类和识别方案
+ TensorFlow 部署
+ DNN：ResNet 50、ResNet 152、VGG-16、SSD-VGG 和 DenseNet-121
+ Intel FPGA 硬件 

使用这种支持 FPGA 的体系结构，受训的神经网络就可以快速运行并降低延迟。 Project Brainwave 可以在 FPGA 之间并行化预先训练的深度神经网络 (DNN) 来横向扩展服务。 DNNs 可以预先训练，可以作为深度特征化器来传输学习内容，或者使用更新的权重进行优化。

### <a name="scenarios-and-applications"></a>方案和应用

Project Brainwave 与 Azure 机器学习集成。 Microsoft 使用 FPGA 进行 DNN 评估、必应搜索排名、软件定义网络 (SDN) 加速，以便将 CPU 解放出来完成其他任务，同时降低延迟。

以下方案在 Project Brainwave 体系结构上使用 FPGA：
+ [自动化光学检查系统](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [地表覆盖制图](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>部署到 Azure 中的 FPGA

下面是在 Azure 中创建图像识别服务的工作流，使用支持的 DNN 作为特征化器在 Azure FPGA 上进行部署：

1. 使用用于 Python 的 Azure 机器学习 SDK 来创建服务定义，该定义是一个文件，用于根据 TensorFlow 来描述图形（输入、特征化器、分类器）的管道。 部署命令会自动将定义和图压缩到 ZIP 文件中，然后将 ZIP 文件上传到 Azure Blob 存储。  DNN 已经部署在 Project Brainwave 上，可以在 FPGA 上运行。

1. 将 SDK 与 Azure Blob 存储中的 ZIP 文件配合使用，以便注册模型。

1. 使用 SDK 通过注册的模型部署服务。

可以根据 **“[将模型部署为 FPGA 上的 Web 服务](how-to-deploy-fpga-web-service.md)”** 一文的说明，开始将受训的 DNN 模型部署到 Azure 云中的 FPGA。


## <a name="next-steps"></a>后续步骤

请访问以下视频和博客：

+ [Hyperscale hardware: ML at scale on top of Azure + FPGA : Build 2018 (video)](https://www.youtube.com/watch?v=BMgQAHIx2eY)（超大规模硬件：基于 Azure + FPGA：内部版本 2018 的大规模机器学习（视频））

+ [Inside the Microsoft FPGA-based configurable cloud (video)](https://channel9.msdn.com/Events/Build/2017/B8063)（深入了解基于 Microsoft FPGA 的可配置云（视频））

+ [用于实时 AI 的 Project Brainwave：项目主页](https://www.microsoft.com/research/project/project-brainwave/)
