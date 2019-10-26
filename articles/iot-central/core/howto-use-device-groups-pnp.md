---
title: 使用 Azure IoT Central 应用程序中的设备组 |Microsoft Docs
description: 作为操作员，如何使用 Azure IoT Central 应用程序中的设备组。
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 91aae53ae81d2bc7cfda54cefa786e4b1a1aab67
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949659"
---
# <a name="use-device-groups-in-your-azure-iot-central-application-preview-features"></a>使用 Azure IoT Central 应用程序中的设备组（预览功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本文介绍如何使用 Azure IoT Central 应用程序中的设备组作为操作员。

设备组是组合在一起的设备列表，因为它们与某些指定的条件相匹配。 设备组通过将设备分组为较小的逻辑组，帮助你按比例管理、可视化和分析设备。 例如，你可以创建一个设备组来列出西雅图的所有 air 空调设备，以使技术人员能够查找其所负责的设备。 本文介绍如何创建和配置设备组。

>  [!NOTE] 
> 对于 Azure IoT Edge 设备，你必须选择 Azure IoT Edge 模板来创建设备组

## <a name="create-a-device-group"></a>创建设备组

若要创建设备组：

1. 选择左窗格中的 "**设备组**"。

1. 选择 " **+ 新建**"。

    ![新设备组](media/howto-use-device-groups-pnp/image1.png)

1. 为设备组指定在整个应用程序中唯一的名称。 还可以添加说明。 设备组只能包含来自单个设备模板的设备。 选择要用于此组的设备模板。

1. 通过选择属性、比较运算符和值，创建查询以确定设备组的设备。 你可以添加多个查询，并将满足**所有**条件的设备放入设备组中。 有权访问该应用程序的任何人都可以访问您创建的设备组，以便任何人都可以查看、修改或删除该设备组。

    ![设备组查询](media/howto-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > 设备组是一个动态查询。 每当查看设备列表时，列表中都可能显示不同的设备。 列表内容取决于哪些设备当前满足查询的条件。

1. 选择“保存”。

## <a name="analytics"></a>分析

设备组中的分析与左窗格中的 "主分析" 选项卡相同。 可以在有关[如何创建分析](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)的文章中详细了解分析。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何使用 Azure IoT Central 应用程序中的设备组，以下是建议的下一个步骤：

> [!div class="nextstepaction"]
> [如何创建遥测规则](howto-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
