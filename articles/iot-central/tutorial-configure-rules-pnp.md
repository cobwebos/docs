---
title: 在 Azure IoT Central 中配置规则和操作 | Microsoft Docs
description: 本教程演示，作为构建者，如何在 Azure IoT Central 应用程序中配置基于遥测的规则和操作。
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: c2aa6edfe7ce9b670ea1015e2da72f3ecc48c9ec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878828"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>教程：在 Azure IoT Central 中配置用于设备的规则和操作（预览功能）

*本文适用于操作员、构建者和管理员。*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

在本教程中，将创建一个规则，用于在环境传感器设备中的温度超过 90 &deg; F 时发送电子邮件。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建基于遥测的规则
> * 添加操作

## <a name="prerequisites"></a>先决条件

在开始之前，应完成[在应用程序中定义新的设备类型](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)教程，以创建要使用的“环境传感器”  设备模板。

## <a name="create-a-telemetry-based-rule"></a>创建基于遥测的规则

1. 若要为应用程序添加新的基于遥测的规则，请在左侧导航菜单中选择“规则”  。

1. 若要创建新规则，请选择“+新建”  。 然后选择“遥测”  。

1. 输入 **Environmental temperature** 作为规则名称。

1. 在“范围”  部分中，选择“环境传感器”  作为设备模板。 此规则适用的设备范围。 可以使用“+筛选器”  添加更多筛选条件。

1. 在“条件”  部分中，定义触发规则的条件。 使用以下信息定义基于温度遥测的条件：

    | 字段                                        | 值                             |
    | -------------------------------------------- | ------------------------------    |
    | 度量                                  | 温度                       |
    | 运算符                                     | 大于                   |
    | 值                                        | 90                                |

    若要添加更多条件，请选择“+条件”  。

    ![创建规则条件](./media/tutorial-configure-rules-pnp/condition.png)

1. 若要添加要在规则触发时运行的操作，请选择“+操作”  ，然后选择“电子邮件”  。

1. 使用下表中的信息定义操作：

    | 设置   | 值                                             |
    | --------- | ------------------------------------------------- |
    | 目标        | 你的电子邮件地址                                |
    | 说明     | 环境温度超过阈值。 |

    > [!NOTE]
    > 若要接收电子邮件通知，电子邮件地址必须是[应用程序中的用户 ID](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)，并且该用户必须至少登录过应用程序一次。

    ![创建规则操作](./media/tutorial-configure-rules-pnp/action.png)

1. 选择“保存”。  你的规则将列在“规则”  页上。

## <a name="test-the-rule"></a>测试规则

保存规则后不久，它将变为活动状态。 满足规则中定义的条件时，应用程序会将消息发送到操作中指定的电子邮件地址。

> [!NOTE]
> 测试完成后，关闭该规则以停止在收件箱中接收警报。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

* 创建基于遥测的规则
* 添加操作

现在，你已定义了基于阈值的规则，建议执行的下一步骤是：

> [!div class="nextstepaction"]
> [创建事件规则并设置通知](howto-create-event-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。
