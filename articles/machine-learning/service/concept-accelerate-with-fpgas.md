---
title: Azure 机器学习和 FPGA
description: 了解如何借助 FPGA 加快模型和深度神经网络的速度。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: 493b3f8de617146af534349e18ed49b83c46dee8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-a-field-programmable-gate-array-fpga"></a>什么是现场可编程门阵列 (FPGA)？

现场可编程门阵列 (FPGA) 是可以根据需要重新配置的集成电路。 可以根据需要更改 FPGA 来实现新逻辑。 Azure 机器学习硬件加速模型允许向 Azure 云中的 FPGA 部署已训练的模型。

此功能由 Project Brainwave 提供支持，该项目将深度神经网络 (DNN) 转变为 FPGA 程序。 

## <a name="why-use-an-fpga"></a>为何使用 FPGA？

FPGA 实现了超低延迟，并且它们在对批大小为一的数据进行评分时尤其有效（不需要采用很大的批大小）。  它们经济高效，并且在 Azure 中可用。  Project Brainwave 可以在这些 FPGA 之间并行化预先训练的 DNN 来横向扩展服务。

## <a name="next-steps"></a>后续步骤

[将模型部署为 FPGA 上的 Web 服务](how-to-deploy-fpga-web-service.md)

[了解如何安装 FPGA SDK](reference-fpga-package-overview.md)