---
title: Azure IoT Central 中的体系结构概念 - 能源 | Microsoft Docs
description: 本文介绍与 Azure IoT Central 能源应用模板的体系结构相关的重要概念
author: op-ravi
ms.author: omravi
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8f3772c1d65780337c421cfaaa7b70d7ac7186cf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024307"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central - 智能仪表应用体系结构



本文概述了监视应用模板体系结构的智能仪表。 下图显示了 Azure 中使用 IoT Central 平台的智能仪表应用的常用体系结构。

> [!div class="mx-imgBorder"]
> ![智能仪表体系结构](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

该体系结构包括以下组件。 一些解决方案可能不需要此处列出的所有组件。

## <a name="smart-meters-and-connectivity"></a>智能仪表和连接 

智能仪表是所有能源资产中最重要的设备之一。 它记录能源消耗数据并将其传输到用于监视和其他用例（如计费和需求响应）的实用程序。 根据仪表类型，它可以通过网关或其他中间设备或系统（例如边缘设备和前端系统）连接到 IoT Central。 构建 IoT Central 设备网桥以连接无法直接连接的设备。 IoT Central 设备桥是一种开放源代码解决方案，你可以在[此处](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge)找到完整的详细信息。 


## <a name="iot-central-platform"></a>IoT Central 平台

Azure IoT Central 是一个平台，可简化 IoT 解决方案的构建，并有助于减轻 IoT 管理、运营和开发的负担以及降低相关成本。 借助 IoT Central，可以轻松地大规模连接、监视和管理物联网 (IoT) 资产。 将智能仪表连接到 IoT Central 之后，应用模板使用设备模型、命令和仪表板等内置功能。 该应用模板还将 IoT Central 存储用于暖路径方案，例如近实时仪表数据监视、分析、规则和可视化效果。 


## <a name="extensibility-options-to-build-with-iot-central"></a>使用 IoT Central 进行构建的可扩展性选项
IoT Central 平台提供了两个扩展选项：连续数据导出 (CDE) 和 API。 客户和合作伙伴可以在这些选项之间进行选择，以针对特定需求自定义其解决方案。 例如我们的某个合作伙伴为 Azure Data Lake Storage (ADLS) 配置了 CDE。 他们将 ADLS 用于长期数据保留和其他冷路径存储方案（例如批处理、审核和报告目的）。 

## <a name="next-steps"></a>后续步骤

* 现在你已了解该体系结构，请[免费创建智能仪表应用](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* 若要详细了解 IoT Central，请参阅 [IoT Central 概述](https://docs.microsoft.com/azure/iot-central/)
