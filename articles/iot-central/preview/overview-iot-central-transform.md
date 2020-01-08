---
title: 转换概述
description: 了解如何转换
author: viv-liu
ms.author: viviali
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 3df839738aaf28de3b32eee6e30f15376a00cc0d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434889"
---
# <a name="transform-your-iot-data-preview-features"></a>转换 IoT 数据（预览功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

作为应用程序平台，IoT Central 提供几个关键方面，以帮助你将 IoT 数据转换为可操作结果带来的业务见解。 IoT Central 提供将 IoT 数据扩展到外部系统以便与业务线应用程序和自定义应用程序建立集成的方法。 可以通过创建通过移动服务通知技术人员的规则来监视设备运行状况和操作。 可以通过将原始遥测数据导出到 Azure 中的服务，使用自定义分析和机器学习来生成特定的业务见解。 你可以使用公共 Api 创建用于监视和控制设备并管理你的 IoT Central 应用的服务和工具。 

![IoT Central 概述中的转换](media/overview-iot-central-transform/transform.png)

## <a name="monitor-device-health-and-operations-using-rules"></a>使用规则监视设备运行状况和操作
计算机连接并发送数据后，可以识别哪些计算机遇到问题或发送错误消息，以便可以将这些计算机固定到正常运行，同时最大限度地减少停机时间。 你可以在 IoT Central 应用中构建规则来监视设备正在发送的遥测数据，并在超过阈值或发送特定事件消息时发出警报。 你可以为规则配置诸如电子邮件操作和 webhook 之类的操作，以通知正确的人员和正确的下游系统。 在此处了解有关规则的详细信息。

## <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>对导出的数据运行自定义分析和处理
可以生成自定义分析管道来处理原始 IoT 遥测并存储最终结果，从而生成高度自定义的业务见解，如确定计算机效率趋势或预测工厂上的未来能耗。 你可以在 IoT Central 应用中创建数据导出，以将遥测数据、设备属性和生命周期以及设备模板更改信息导出到其他 Azure 服务，以便你可以在最有价值的工具中分析、存储和可视化数据。 在此处了解有关数据导出的详细信息。

## <a name="build-custom-iot-solutions-and-integrations-with-apis"></a>构建自定义 IoT 解决方案和与 Api 的集成
你可以构建 IoT 解决方案，如可以远程控制设备和设置新设备的移动应用程序，或与现有业务线应用程序的自定义集成，以便与你的 IoT 设备和针对你的业务定制的数据进行交互。 你可以使用 IoT Central 作为设备建模、载入、管理和数据访问的主干。 详细了解此学习模块中的公共 API。
