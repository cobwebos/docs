---
title: 在 Azure IoT Central 中配置规则和操作 | Microsoft Docs
description: 本教程演示，作为构建者，如何在 Azure IoT Central 应用程序中配置基于遥测的规则和操作。
author: ankitgupta
ms.author: ankitgup
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: fbe9e1fbd0891f2f39b05fa7ba53653188ef8e03
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158068"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>教程：在 Azure IoT Central 中配置用于设备的规则和操作

*本文适用于操作员、构建者和管理员。*

在本教程中，将创建一个规则，用于在连接的空调设备中的温度超过 90 &deg; F 时发送电子邮件。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建基于遥测的规则
> * 添加操作

## <a name="prerequisites"></a>先决条件

在开始之前，应完成[在应用程序中定义新的设备类型](tutorial-define-device-type.md)教程，以创建要使用的“已连接空调”设备模板。

## <a name="create-a-telemetry-based-rule"></a>创建基于遥测的规则

1. 若要为应用程序添加新的基于遥测的规则，请在左侧导航菜单中选择 **Device Explorer**：

    ![Device Explorer 页](media/tutorial-configure-rules/explorerpage1.png)

    可看到在上一教程中创建的“已连接空调 (1.0.0)”设备模板和“已连接空调-1”设备。

2. 若要开始自定义连接的空调设备，请选择在上一教程中创建的设备：

    ![“已连接空调”页](media/tutorial-configure-rules/builderdevicelist1.png)

3. 若要开始在“规则”视图中添加规则，请选择“规则”，然后单击“编辑模板”：

    ![“规则”视图](media/tutorial-configure-rules/builderedittemplate.png)

4. 若要创建基于阈值的遥测规则，请单击“新建规则”，然后选择“遥测”。

    ![编辑模板](media/tutorial-configure-rules/buildernewrule.png)

5. 若要定义规则，请使用下表中的信息：

    | 设置                                      | 值                             |
    | -------------------------------------------- | ------------------------------    |
    | 名称                                         | 空调温度警报 |
    | 为此此模板中的所有设备启用规则 | 启用                                |
    | 在此设备上启用规则                   | 启用                                |
    | 条件                                    | 温度大于 90    |
    | 聚合                                  | 无                              |

    ![温度规则条件](media/tutorial-configure-rules/buildertemperaturerule1.png)

## <a name="add-an-action"></a>添加操作

定义规则时，还需要定义在符合规则条件时要运行的操作。 在本教程中，将添加一个操作以发送电子邮件，作为触发规则的通知。

1. 若要添加操作，首先请“保存”规则，然后在“配置遥测规则”面板中向下滚动并选择“操作”旁边的 +，然后选择“电子邮件”：

    ![温度规则操作](media/tutorial-configure-rules/builderaddaction1.png)

2. 若要定义操作，请使用下表中的信息：

    | 设置   | 值                          |
    | --------- | ------------------------------ |
    | 目标        | 你的电子邮件地址             |
    | 说明     | 空调温度超过阈值。 |

    > [!NOTE]
    > 若要接收电子邮件通知，电子邮件地址必须是[应用程序中的用户 ID](howto-administer.md)，并且该用户必须至少登录过应用程序一次。

    ![应用程序构建者温度操作](media/tutorial-configure-rules/buildertemperatureaction.png)

3. 选择“保存”。 你的规则将列在“规则”页上：

    ![应用程序构建者规则](media/tutorial-configure-rules/builderrules1.png)

4. 选择“完成”，退出“编辑模板”模式。
 

## <a name="test-the-rule"></a>测试规则

保存规则后不久，它将变为活动状态。 满足规则中定义的条件时，应用程序会将消息发送到操作中指定的电子邮件地址。

![电子邮件操作](media/tutorial-configure-rules/email.png)

> [!NOTE]
> 测试完成后，请关闭角色，停止在收件箱中接收警报。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * 创建基于遥测的规则
> * 添加操作

现在已定义了基于阈值的规则，建议的下一步是[自定义操作员的视图](tutorial-customize-operator.md)。

若要详细了解 Azure IoT Central 中不同类型的规则以及如何参数化规则定义，请参阅：
* [创建遥测规则并设置通知](howto-create-telemetry-rules.md)。
* [创建事件规则并设置通知](howto-create-event-rules.md)。

<!-- Next tutorials in the sequence -->
