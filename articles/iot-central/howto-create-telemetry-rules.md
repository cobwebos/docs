---
title: 在 Azure IoT Central 应用程序中创建和管理遥测规则 | Microsoft Docs
description: 可以通过 Azure IoT Central 遥测规则近乎实时地监视设备并自动调用操作（例如在触发规则时发送电子邮件）。
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: caca4e9db898b3766995fde8c5eebd4767abd85b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629807"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>在 Azure IoT Central 应用程序中创建遥测规则并设置通知

可以使用 Microsoft Azure IoT Central 对连接的设备进行远程监视。 可以通过 Azure IoT Central 规则近乎实时地监视设备并自动调用操作（例如在触发规则时发送电子邮件）。 只需几次单击即可定义用于监视设备数据的条件并配置要调用的操作。 本文详细介绍遥测规则。

Azure IoT Central 使用[遥测度量](howto-set-up-template.md)来捕获设备数据。 每种类型的度量都有用于定义度量的关键属性。 可以创建规则来监视每种类型的设备度量，在触发规则时生成警报。 当选定的设备遥测超过指定的阈值时，会触发遥测规则。

## <a name="create-a-telemetry-rule"></a>创建遥测规则

本部分介绍如何创建遥测规则。 此示例使用一个发送温度和湿度遥测数据的连接空调设备。 该规则监视设备报告的温度，并在温度超过 80 度时发送电子邮件。

1. 导航到要向其添加规则的设备所对应的设备详细信息页。

1. 如果尚未创建任何规则，则会看到以下屏幕：

    ![尚无规则](media\howto-create-telemetry-rules\image1.png)

1. 在“规则”选项卡上选择“+ 新建规则”可以查看能够创建的规则类型。

    ![规则类型](media\howto-create-telemetry-rules\image2.png)

1. 选择“遥测”磁贴可以打开用于创建规则的窗体。

    ![遥测规则](media\howto-create-telemetry-rules\image3.png)

1. 选择一个有助于在此设备模板中识别该规则的名称。

1. 若要立即为所有根据此模板创建的设备启用此规则，请切换“启用规则”。

### <a name="configure-the-rule-condition"></a>配置规则条件

本部分介绍如何添加一个条件用于监视温度遥测数据。

1. 选择“条件”旁边的“+”。

1. 从下拉列表中选择“温度”遥测类型。 然后选择运算符并提供阈值。 可以添加多个遥测条件。 如果指定多个条件，必须满足所有条件才能触发该规则。

    ![添加遥测条件](media\howto-create-telemetry-rules\image4.png)

    > [!NOTE]
    > 定义遥测规则条件时，请至少选择一个遥测度量。

### <a name="configure-the-action"></a>配置操作

本部分介绍如何通过添加操作来指定在满足条件的情况下规则所执行的操作。

1. 选择“操作”旁边的“+”。 在此处可以看到可用操作的列表。 在公共预览版发布期间，唯一支持的操作是“电子邮件”。

    ![添加操作](media\howto-create-telemetry-rules\image5.png)

1. 选择“电子邮件”操作，在“收件人”字段中输入有效的电子邮件地址，并提供一个说明，用于在触发规则时显示在电子邮件的正文中。

    > [!NOTE]
    > 电子邮件只发送给那些已添加到应用程序中并已至少登录一次的用户。 详细了解 Azure IoT Central 中的[用户管理](howto-administer.md)。

   ![配置操作](media\howto-create-telemetry-rules\image6.png)

1. 若要保存规则，请选择“保存”。 此规则在数分钟内即可生效，然后开始监视发送到应用程序的遥测数据。 匹配规则中指定的条件时，规则会触发配置的电子邮件操作。

## <a name="parameterize-the-rule"></a>将规则参数化

规则可以从用作参数的**设备属性**派生某些值。 如果不同设备的遥测阈值不同，则使用参数会很有帮助。 创建规则时，请选择一个指定阈值（例如“最大理想阈值”）的设备属性，而不要提供绝对值（例如 80 度）。 当规则执行时，会将设备遥测数据与设备属性中提供的值相匹配。

使用参数能够有效地减少要为每个设备模板管理的规则数目。

也可以使用“设备属性”作为参数来配置操作。 如果将电子邮件地址存储为设备属性，则可在定义“收件人”地址时使用它。

## <a name="delete-a-rule"></a>删除规则

如果不再需要某项规则，可打开该规则并选择“删除”，以将其删除。 删除规则会将其从设备模板和所有关联的设备中删除。

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>启用或禁用设备模板的规则

导航到设备，然后选择要启用或禁用的规则。 切换规则中的“为此模板的所有设备启用规则”按钮可为与设备模板关联的所有设备启用或禁用该规则。

## <a name="enable-or-disable-a-rule-for-a-device"></a>启用或禁用设备的规则

导航到设备，然后选择要启用或禁用的规则。 切换“启用此设备的规则”按钮可启用或禁用该设备的规则。

## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中编辑规则后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [如何管理设备](howto-manage-devices.md)。