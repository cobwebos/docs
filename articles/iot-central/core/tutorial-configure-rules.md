---
title: 教程：在 Azure IoT Central 中配置规则和操作
description: 本教程演示，作为构建者，如何在 Azure IoT Central 应用程序中配置基于遥测的规则和操作。
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 9140114e7d31f24770bdcce9aae849b01aae9996
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263595"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>教程：在 Azure IoT Central 中配置用于设备的规则和操作

*本文适用于操作员、构建者和管理员。*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

在本教程中，将创建一个规则，用于在连接的空调设备中的温度超过 70&deg; F 时发送电子邮件。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 创建基于遥测的规则
> * 添加操作

## <a name="prerequisites"></a>必备条件

在开始之前，应完成[在应用程序中定义新的设备类型](tutorial-define-device-type.md)教程，以创建要使用的“已连接空调”  设备模板。

## <a name="create-a-telemetry-based-rule"></a>创建基于遥测的规则

1. 若要为应用程序添加新的基于遥测的规则，请在左侧窗格中选择“设备模板”  ：

    ![“设备模板”页](media/tutorial-configure-rules/templatespage1.png)

    可以看到在上一教程中创建的“已连接空调 (1.0.0)”  设备模板。

2. 若要自定义设备模板，请选择在上一教程中创建的“已连接空调”  模板。

3. 若要在“规则”  视图中添加基于遥测的规则，请依次选择“规则”  、“+ 新建规则”  和“遥测”  ：

    ![“规则”视图](media/tutorial-configure-rules/newrule.png)

4. 若要定义规则，请使用下表中的信息：

    | 设置                                      | 值                             |
    | -------------------------------------------- | ------------------------------    |
    | 名称                                         | 空调温度警报 |
    | 为此此模板中的所有设备启用规则 | 启用                                |
    | 条件                                    | 温度大于 70    |
    | 聚合                                  | 无                              |

    ![温度规则条件](media/tutorial-configure-rules/temperaturerule.png)

    再选择“保存”  。

## <a name="add-an-action"></a>添加操作

定义规则时，还需要定义在符合规则条件时要运行的操作。 在本教程中，你将创建一个规则，使其包含用于发送电子邮件通知的操作。

1. 若要添加操作，请首先保存规则，然后在“配置遥测规则”面板中向下滚动。    选择“操作”  旁边的 **+** ，然后选择“电子邮件”  ：

    ![温度规则操作](media/tutorial-configure-rules/addaction.png)

2. 若要定义操作，请使用下表中的信息：

    | 设置      | 值                                               |
    | ------------ | --------------------------------------------------- |
    | 显示名称 | 通过电子邮件发送温度警报                             |
    | 目标           | 你的电子邮件地址                                  |
    | 说明        | 空调温度超过阈值。 |

    > [!NOTE]
    > 若要接收电子邮件通知，电子邮件地址必须是[应用程序中的用户 ID](howto-administer.md)，并且该用户必须至少登录过应用程序一次。

    ![温度操作](media/tutorial-configure-rules/temperatureaction.png)

3. 选择“保存”。  你的规则将列在“规则”  页上。

## <a name="test-the-rule"></a>测试规则

保存规则后不久，它将变为活动状态。 满足规则中定义的条件时，应用程序会将消息发送到操作中指定的电子邮件地址。

> [!NOTE]
> 测试完成后，关闭该规则以停止在收件箱中接收警报。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
>
> * 创建基于遥测的规则
> * 添加操作

现在，你已定义了基于阈值的规则，建议执行的下一步骤是[自定义操作员的视图](tutorial-customize-operator.md)。

若要详细了解 Azure IoT Central 中不同类型的规则以及如何参数化规则定义，请参阅：

* [创建遥测规则并设置通知](howto-create-telemetry-rules.md)。
* [创建事件规则并设置通知](howto-create-event-rules.md)。

<!-- Next tutorials in the sequence -->