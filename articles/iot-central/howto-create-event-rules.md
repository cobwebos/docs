---
title: 在 Azure IoT Central 应用程序中创建和管理事件规则 | Microsoft Docs
description: 可以通过 Azure IoT Central 事件规则以近实时方式监视设备并自动调用操作（例如在触发规则时发送电子邮件）。
services: iot-central
author: ankitgupta
ms.author: ankitgup
ms.date: 04/29/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 932b1906b767ee7676f46ffd7242ad3d478d41c2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201585"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>在 Azure IoT Central 应用程序中创建事件规则并设置通知

可以使用 Microsoft Azure IoT Central 对连接的设备进行远程监视。 可以通过 Azure IoT Central 规则以近实时方式监视设备并自动调用操作（例如在触发规则时发送电子邮件）。 只需几次单击即可定义用于监视设备数据的条件并配置要调用的操作。 本文详细介绍事件监视规则。

Azure IoT Central 使用[事件度量](howto-set-up-template.md)来捕获设备数据。 每种类型的度量都有用于定义度量的关键属性。 可以创建规则来监视每种类型的设备度量，在触发规则时生成警报。 当设备报告所选设备事件时，会触发事件规则。

## <a name="create-an-event-rule"></a>创建事件中心

此部分介绍如何创建事件规则。 此示例使用冷藏食品贩卖机设备来报告风扇电机错误事件。 此规则监视由设备报告的事件，只要报告了事件，就会发送电子邮件。

1. 导航到要向其添加规则的设备所对应的设备详细信息页。

1. 如果尚未创建任何规则，则会看到以下屏幕：

    ![尚无规则](media\howto-create-event-rules\image1.png)

1. 在“规则”选项卡上选择“+ 新建规则”即可查看能够创建的规则的类型。

    ![规则类型](media\howto-create-event-rules\image2.png)

1. 单击“事件”即可打开用于创建规则的窗体。

    ![事件规则](media\howto-create-event-rules\image3.png)

1. 选择一个名称，以便确定此设备模板中的规则。

1. 若要立即为所有根据此模板创建的设备启用此规则，请切换到“启用规则”。

### <a name="configure-the-rule-condition"></a>配置规则条件

此部分介绍如何添加一项用于监视风扇电机错误事件度量的条件。

1. 选择“条件”旁边的“+”。

1. 从下拉列表中选择要监视的事件度量。 在此示例中，已选择“风扇电机错误”事件。

1. （可选）如果需要监视设备所报告事件的特定值，则也可提供一个值。 例如，如果设备使用不同的错误代码来报告同一事件，则可在规则的条件中以值的形式提供错误代码，这样可确保规则仅在设备以事件负载形式发送该特定值的情况下触发。 将此项保留为空白意味着，只要设备发送事件，不管事件值如何，都会触发规则。

    ![添加事件条件](media\howto-create-event-rules\image4.png)

    > [!NOTE]
    > 在定义事件规则条件时，必须选择至少一个事件度量。

### <a name="configure-the-action"></a>配置操作

此部分介绍如何通过添加操作来指定在满足条件的情况下规则所执行的操作。

1. 选择“操作”旁边的“+”。 在这里可以看到可用操作的列表。 在公共预览版发布期间，唯一支持的操作是“电子邮件”。

    ![添加操作](media\howto-create-event-rules\image5.png)

1. 选择“电子邮件”操作，在“收件人”字段中输入有效的电子邮件地址，并提供一个说明，用于在触发规则时显示在电子邮件的正文中。

    > [!NOTE]
    > 电子邮件只发送给那些已添加到应用程序中并已至少登录一次的用户。 详细了解 Azure IoT Central 中的[用户管理](howto-administer.md)。

   ![配置操作](media\howto-create-event-rules\image6.png)

1. 若要保存规则，请选择“保存”。 此规则数分钟即可生效，然后就开始监视发送到应用程序的事件。 当规则中指定的条件匹配时，规则会触发配置的电子邮件操作。

## <a name="parameterize-the-rule"></a>将规则参数化

也可使用“设备属性”作为参数来配置操作。 如果将电子邮件地址存储为设备属性，则可在定义“收件人”地址时使用它。

## <a name="delete-a-rule"></a>删除规则

如果不再需要某项规则，则可打开该规则并选择“删除”，以便将其删除。 删除规则会将其从设备模板和所有关联的设备中删除。

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>启用或禁用设备模板的规则

导航到设备，然后选择要启用或禁用的规则。 切换规则中的“为此模板的所有设备启用规则”按钮即可为与设备模板关联的所有设备启用或禁用该规则。

## <a name="enable-or-disable-a-rule-for-a-device"></a>启用或禁用设备的规则

导航到设备，然后选择要启用或禁用的规则。 切换“启用此设备的规则”按钮即可启用或禁用该设备的规则。

## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中创建规则后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [如何管理设备](howto-manage-devices.md)。