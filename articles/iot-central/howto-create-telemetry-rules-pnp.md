---
title: 在 Azure IoT Central 应用程序中创建和管理遥测规则 | Microsoft Docs
description: 可以通过 Azure IoT Central 遥测规则近乎实时地监视设备并自动调用操作（例如在触发规则时发送电子邮件）。
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d6deecf558105701be591c1f08923eca2c1e3bbd
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879937"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>在 Azure IoT Central 应用程序中创建遥测规则并设置通知 (预览功能)

*本文适用于操作员、构建者和管理员。*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

可以使用 Azure IoT Central 对连接的设备进行远程监视。 可以通过 Azure IoT Central 规则近乎实时地监视设备并自动调用操作（例如发送电子邮件或触发 Microsoft Flow）。 只需几次单击即可定义用于监视设备数据的条件并配置相应操作。 本文介绍如何创建规则来监视设备发送的遥测数据。

设备可以使用遥测度量从设备发送数字数据。 当选定的设备遥测超过指定的阈值时，会触发遥测规则。

## <a name="create-a-telemetry-rule"></a>创建遥测规则

若要创建遥测规则, 设备定义必须至少定义一个遥测度量值。 本示例使用一个发送温度和湿度遥测数据的冷冻贩卖机设备。 该规则监视设备报告的温度，并在温度超过 80 度时发送电子邮件。

1. 导航到 "**规则**" 页。

1. 如果尚未创建任何规则, 则会看到以下屏幕:

    ![尚无规则](media/howto-create-telemetry-rules-pnp/rules-landing-page1.png)

1. 选择 " **+ 新建规则**" 以查看可以创建的规则类型。

1. 选择 "**遥测**" 创建用于监视设备遥测的规则。

    ![规则类型](media/howto-create-telemetry-rules-pnp/rule-types1.png)


1. 输入一个名称, 用于识别规则并按 Enter。

1. 在 "作用域" 部分中, 选择要将此规则限定到的设备定义。 在此屏幕中, 你可以使用 **+ 筛选**器来筛选应用规则的设备。 此规则自动应用到该设备模板下的所有设备。 若要禁用规则, 请在页眉中选择 "**禁用**" 按钮。

### <a name="configure-the-rule-conditions"></a>配置规则条件

条件用于定义此规则监视的条件。

1. 选择是否要**将聚合设置**为 on 或 off。

      - 聚合是可选的。 如无聚合，此规则将对每个满足此条件的遥测数据点触发。 例如, 如果将规则配置为在温度高于80时触发, 则当设备报告温度 > 80 时, 规则几乎立即触发。
      - 如果选择了聚合函数 (如 average、min、max、count), 则用户必须提供一个时间范围, 需要在该**时间范围**内对条件进行评估。 例如，如果将时段设置为“5 分钟”，并且规则查找超过 80 的平均温度，则当平均温度至少有 5 分钟超过 80 时，该规则将会触发。 规则评估频率与**时间范围**相同, 这意味着在此示例中, 每5分钟计算一次规则。

1. 从“度量”下拉列表中选择要监视的遥测数据。

1. 接下来, 选择一个**运算符**, 并提供一个**值**。

     ![条件](media/howto-create-telemetry-rules-pnp/aggregate-condition-filled-out1.png)


>[!NOTE]
>可以通过选择 " **+ 条件**" 来添加多个遥测度量。 如果指定多个条件，必须满足所有条件才能触发该规则。 每个条件都由 "AND" 子句隐式联接。 使用聚合时，必须聚合每个度量。

### <a name="configure-actions"></a>配置操作

本部分演示如何设置在规则触发时要执行的操作。 此规则中指定的所有条件评估结果均为 true 时会调用操作。

1. 单击 "**操作**" 部分中的 **+ 操作**。 在此处可以看到可用操作的列表。  

    ![添加操作](media/howto-create-telemetry-rules-pnp/add-action1.png)


1. 选择 "**电子邮件**" 操作, 为操作输入显示名称, 在 "**到**" 字段中输入有效的电子邮件地址, 并提供在规则触发时要在电子邮件正文中显示的注释。

    > [!NOTE]
    > 电子邮件只发送给那些已添加到应用程序中并已至少登录一次的用户。 详细了解 Azure IoT Central 中的[用户管理](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。

   ![配置操作](media/howto-create-telemetry-rules-pnp/configure-action1.png)


1. 若要保存该操作, 请选择 "**完成**"。

1. 若要保存规则，请选择“保存”。 此规则在数分钟内即可生效，然后开始监视发送到应用程序的遥测数据。 满足规则中指定的条件时，规则会触发配置的电子邮件操作。

## <a name="parameterize-the-rule"></a>将规则参数化

规则可以从用作参数的**设备属性**派生某些值。 如果不同设备的遥测阈值不同，则使用参数会很有帮助。 创建规则时, 请选择一个指定阈值 (例如**最大理想阈值**) 的设备属性, 而不是提供绝对值, 如80度。 当规则执行时，会将设备遥测数据与设备属性中设置的值相匹配。

使用参数是减少要管理的规则数量的有效方法。

也可以使用“设备属性”作为参数来配置操作。 如果将电子邮件地址存储为属性，则可在定义“收件人”地址时使用它。

## <a name="delete-a-rule"></a>删除规则

如果不再需要某项规则，可打开该规则并选择“删除”，以将其删除。 删除规则会将其从设备模板和所有关联的设备中删除。

## <a name="enable-or-disable-a-rule"></a>启用或禁用规则

选择要启用或禁用的规则。 切换规则中的 "**启用**" 或 "**禁用**" 按钮, 为规则范围内的所有设备启用或禁用规则。

## <a name="enable-or-disable-a-rule-for-a-device"></a>启用或禁用设备的规则

选择要启用或禁用的规则。 在 "**作用域**" 部分中添加筛选器, 以在设备模板中包含或排除特定设备。

## <a name="next-steps"></a>后续步骤

现在, 你已了解如何在 Azure IoT Central 应用程序中创建规则, 下一步是了解[如何管理你的设备](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
