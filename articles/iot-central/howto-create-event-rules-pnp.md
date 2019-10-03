---
title: 在 Azure IoT Central 应用程序中创建和管理事件规则 | Microsoft Docs
description: 可以通过 Azure IoT Central 事件规则以近实时方式监视设备并自动调用操作（例如在触发规则时发送电子邮件）。
author: dominicbetts
ms.author: dobett
ms.date: 07/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1a7fc2b38e8354fb29255bb7f9d6b2c03ed53725
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879950"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>在 Azure IoT Central 应用程序中创建事件规则并设置通知 (预览功能)

*本文适用于操作员、构建者和管理员。*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

可以使用 Azure IoT Central 对连接的设备进行远程监视。 Azure IoT Central 规则使你可以近乎实时地监视设备, 并自动调用操作, 如发送电子邮件。 只需几次单击即可定义用于监视设备数据的条件并配置相应操作。 本文介绍如何创建规则来监视设备发送的事件。

设备可以使用事件度量来发送重要或信息性设备事件。 当设备报告所选设备事件时，会触发事件规则。

## <a name="create-an-event-rule"></a>创建事件中心

若要创建事件规则，设备模板必须至少定义有一个事件度量。 此示例使用冷藏食品贩卖机设备来报告风扇电机错误事件。 此规则监视由设备报告的事件，只要报告了事件，就会发送电子邮件。

1. 导航到 "**规则**" 页。

1. 如果尚未创建任何规则，则会看到以下屏幕：

   ![尚无规则](media/howto-create-event-rules-pnp/rules-landing-page1.png)

1. 选择 " **+ 新建规则**" 以查看可以创建的规则类型。

1. 选择 "**事件**" 以创建事件监视规则。

   ![规则类型](media/howto-create-event-rules-pnp/rule-types1.png)

1. 输入一个有助于识别规则的名称, 然后按 Enter。

1. 在 "**作用域**" 部分中选择要将此规则限定到的设备定义。 在此屏幕中, 你可以使用 **+ 筛选**器来筛选应用规则的设备。 此规则自动应用到该设备模板下的所有设备。 若要禁用规则, 请在页眉中选择 "**禁用**" 按钮。

### <a name="configure-the-rule-conditions"></a>配置规则条件

条件用于定义此规则监视的条件。

1. 选择是否要**将聚合设置**为 on 或 off。

   - 如无聚合，此规则对每个满足此条件的事件数据点均会触发。 例如, 如果将该规则的条件配置为在出现**风扇电动机错误**事件时触发, 则该规则在设备报告该事件时几乎立即触发。
   - 如果**Count**用作聚合函数, 则必须提供一个**值**和一个需要计算条件的**时间范围**。 在这种情况下, 将聚合事件计数, 并且仅当聚合事件计数与值匹配时才触发规则。

1. 从 "**度量值**" 下拉列表中选择要监视的事件。 在此示例中，已选择“风扇电机错误”事件。

1. 还可以根据需要将 "**计数**" 设置为 "**聚合**", 并提供规则将触发的值。

     例如, 如果想要在5分钟内有三个以上的设备事件时发出警报, 请选择该事件, 并将聚合函数设置为**Count**, 运算符作为**大于**, 并将**值**设置为3。 将**时间窗口**设置为**5 分钟**。 设备在 5 分钟内发送超过 3 个事件时，此规则会触发。 规则评估频率与**时间范围**相同, 这意味着在此示例中, 每5分钟计算一次规则。

 ![条件](media/howto-create-event-rules-pnp/aggregate-condition-filled-out1.png)

> [!NOTE]
> “条件”下可添加多个事件度量。 如果指定多个条件，必须满足所有条件才能触发该规则。 每个条件都由 "AND" 子句隐式联接。 使用聚合时，必须聚合每个度量。

### <a name="configure-actions"></a>配置操作

本部分演示如何设置在规则触发时要执行的操作。 此规则中指定的所有条件评估结果均为 true 时会调用操作。

1. 单击 "**操作**" 部分中的 **+ 操作**。 在此处可以看到可用操作的列表。  

   ![添加操作](media/howto-create-event-rules-pnp/add-action1.png)

1. 选择 "**电子邮件**" 操作, 为操作输入显示名称, 在 "**到**" 字段中输入有效的电子邮件地址, 并提供在规则触发时要在电子邮件正文中显示的注释。

   > [!NOTE]
   > 电子邮件只发送给那些已添加到应用程序中并已至少登录一次的用户。 详细了解 Azure IoT Central 中的[用户管理](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。

   ![配置操作](media/howto-create-event-rules-pnp/configure-action1.png)

1. 若要保存该操作, 请选择 "**完成**"。

1. 若要保存规则，请选择“保存”。 此规则数分钟即可生效，然后就开始监视发送到应用程序的事件。 满足规则中指定的条件时，规则会触发配置的电子邮件操作。

## <a name="parameterize-the-rule"></a>将规则参数化

规则可以从用作参数的**设备属性**派生某些值。 在事件阈值不同于不同设备的情况下, 使用参数会很有帮助。 创建规则时, 请选择一个指定阈值 (例如**最大理想阈值**) 的设备属性, 而不是提供一个绝对值, 如3个事件。 当规则执行时, 它会将设备事件与设备属性中设置的值进行匹配。

使用参数是减少要管理的规则数量的有效方法。

也可以使用“设备属性”作为参数来配置操作。 如果将电子邮件地址存储为设备属性，则可在定义“收件人”地址时使用它。

## <a name="delete-a-rule"></a>删除规则

如果不再需要某项规则，可打开该规则并选择“删除”，以将其删除。 删除规则会将其从设备模板和所有关联的设备中删除。

## <a name="enable-or-disable-a-rule"></a>启用或禁用规则

选择要启用或禁用的规则。 切换规则中的 "**启用**" 或 "**禁用**" 按钮, 为规则范围内的所有设备启用或禁用规则。

## <a name="enable-or-disable-a-rule-for-a-device"></a>启用或禁用设备的规则

选择要启用或禁用的规则。 在 "**作用域**" 部分中添加筛选器, 以在设备模板中包含或排除特定设备。

## <a name="next-steps"></a>后续步骤

现在, 你已了解如何在 Azure IoT Central 应用程序中创建规则, 下一步是了解[如何管理你的设备](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
