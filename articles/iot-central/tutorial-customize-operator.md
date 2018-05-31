---
title: 在 Azure IoT Central 自定义操作员的视图 | Microsoft Docs
description: 以构建人员的身份在 Azure IoT Central 应用程序中自定义操作员的视图。
services: iot-central
author: sandeeppujar
ms.author: sadeepu
ms.date: 04/16/2018
ms.topic: tutorial
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 3e0dfab05fc7972a055853af45f0d1b13d52c0a1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202316"
---
# <a name="3---customize-the-azure-iot-central-operators-view"></a>3 - 自定义 Azure IoT Central 操作员的视图

本教程向构建人员介绍如何自定义应用程序的操作员视图。 以构建人员的身份对应用程序进行更改时，可在 Microsoft Azure IoT Central 应用程序中预览操作员的视图。

在本教程中，我们将应用程序自定义为向操作员显示有关连接的空调设备的信息。 完成自定义后，操作员可以管理连接到应用程序的空调设备。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 配置设备仪表板
> * 配置设备设置布局
> * 配置设备属性布局
> * 以操作员身份预览设备
> * 配置默认主页
> * 以操作员身份预览默认主页

## <a name="prerequisites"></a>先决条件

在开始之前，应完成前面的两篇教程：

1. [在 Azure IoT Central 应用程序中定义新设备类型](tutorial-define-device-type.md)。
1. [配置设备的规则和操作](tutorial-configure-rules.md)。

## <a name="configure-your-device-dashboard"></a>配置设备仪表板

构建人员可以定义要在设备仪表板上显示的信息。 在[在应用程序中定义新设备类型](tutorial-define-device-type.md)教程中，我们已将一个折线图和其他信息添加到“连接的空调 - 1”仪表板。

1. 若要编辑“连接的空调”设备模板，请在左侧导航菜单中选择“Explorer”：

    ![Explorer 页](media/tutorial-customize-operator/explorer.png)

1. 若要开始自定义连接的空调设备仪表板，请选择“连接的空调(1.0.0)”设备模板。 选择[在应用程序中定义新设备类型](tutorial-define-device-type.md)教程中创建的“连接的空调 - 1”设备：

    ![选择连接的空调设备](media/tutorial-customize-operator/selectdevice.png)

    对某个设备（例如“连接的空调 - 1”）进行更改时，会对基础模板进行更改。 有关详细信息，请参阅[创建新设备模板版本](howto-version-devicetemplate.md)。

1. 若要编辑仪表板，请选择“仪表板”：

    ![设备模板仪表板页](media/tutorial-customize-operator/dashboard.png)

1. 若要将 KPI 磁贴添加到仪表板，请选择“KPI”：

    ![添加 KPI](media/tutorial-customize-operator/addkpi.png)

    若要定义 KPI，请使用下表中的信息：

    | 设置     | 值 |
    | ----------- | ----- |
    | 名称        | 最高温度 |
    | 度量 | 温度 |
    | 聚合 | 最大值 |
    | 时间范围  | 过去 1 周 |

1. 选择“保存”。 现在，可在仪表板中看到“KPI”磁贴：

    ![“KPI”磁贴](media/tutorial-customize-operator/temperaturekpi.png)

1. 若要移动仪表板上的某个磁贴或调整其大小，请将鼠标指针移到该磁贴上。 可将磁贴拖到新位置，或调整其大小：

    ![编辑仪表板布局](media/tutorial-customize-operator/dashboardlayout.png)

## <a name="configure-your-settings-layout"></a>配置设置布局

构建人员还可以配置设备设置的操作员视图。 操作员使用设备设置页来配置设备。 例如，操作员可以使用设置页来设置冷冻机的目标温度。

1. 若要编辑连接的空调的设置布局，请选择“设置”：

    ![“设置”页](media/tutorial-customize-operator/settings.png)

1. 可以移动设置磁贴并调整其大小：

    ![编辑设置布局](media/tutorial-customize-operator/settingslayout.png)

> [!NOTE]
> 在“设计模式”下，无法编辑设置值。

## <a name="configure-your-properties-layout"></a>配置属性布局

除了仪表板和设置以外，还可以配置设备属性的操作员视图。 操作员使用设备属性页来管理设备元数据。 例如，操作员可以使用属性页来查看设备序列号，或更新制造商的联系详细信息。

1. 若要编辑连接的空调的属性布局，请选择“属性”：

    ![“属性”页](media/tutorial-customize-operator/properties.png)

1. 可以移动属性字段并调整其大小：

    ![编辑属性布局](media/tutorial-customize-operator/propertieslayout.png)

> [!NOTE]
> 在“设计模式”下，无法编辑属性值。

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>以操作员身份预览连接的空调设备

在“设计模式”下，可以自定义操作员的仪表板、设置和属性页。 如果关闭“设计模式”，则可以用操作员的身份查看应用程序。

1. 若要以操作员的身份查看连接的空调设备，需要关闭“设计模式”。 若要关闭“设计模式”，请在页面右上角将“设计模式”切换到关闭状态。

1. 若要更新此设备的序列号，请编辑序列号磁贴中的值，并选择“保存”：

    ![编辑属性值](media/tutorial-customize-operator/editproperty.png)

1. 若要将设置发送到连接的空调，请选择“设置”，更改磁贴中的设置值，并选择“更新”：

    ![将设置发送到设备](media/tutorial-customize-operator/sendsetting.png)

    当设备确认新设置值时，该设置会在磁贴上显示为“已同步”。

1. 操作员可以查看构建人员配置的设备仪表板：

    ![设备仪表板的操作员视图](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>配置默认主页

构建人员或操作员在登录到 Azure IoT Central 应用程序时，会看到主页。 构建人员可以配置此主页的内容，以包含对操作员最有用且最相关的内容。

1. 若要自定义默认主页，请导航到“主页”页，并在页面右上角打开“设计模式”。 打开“设计模式”后，一个面板将从右侧滑出，其中包含可添加到主页的对象列表。

    ![应用程序构建人员页](media/tutorial-customize-operator/builderhome.png)

1. 若要自定义主页，请从“库”中添加磁贴。 选择“链接”，并添加组织网站的详细信息。 然后选择“保存”：

    ![添加主页的链接](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > 还可以在 Azure IoT Central 应用程序内部添加页面的链接。 例如，可以添加设备仪表板或设置页的链接。

1. （可选）选择“图像”，并上传要在主页上显示的图像。 图像可以包含 URL，单击该图像时可导航到该 URL：

    ![将图像添加到主页](media/tutorial-customize-operator/addimage.png)

    有关详细信息，请参阅[如何准备图像并将其上传到 Azure IoT Central 应用程序](howto-prepare-images.md)。

## <a name="preview-the-default-home-page-as-an-operator"></a>以操作员身份预览默认主页

若要以操作员身份预览主页，请在页面右上角关闭“设计模式”：

![切换设计模式](media/tutorial-customize-operator/operatorviewhome.png)

可以用构建人员的身份单击链接和图像磁贴，导航到设置的 URL。

## <a name="next-steps"></a>后续步骤

本教程已介绍如何自定义应用程序的操作员视图。

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * 配置设备仪表板
> * 配置设备设置布局
> * 配置设备属性布局
> * 以操作员身份预览设备
> * 配置默认主页
> * 以操作员身份预览默认主页

了解如何自定义应用程序的操作员视图后，建议接下来执行以下后续步骤：

* [监视设备（以操作员的身份）](tutorial-monitor-devices.md)
* [将新设备添加到应用程序（以操作员和设备开发人员的身份）](tutorial-add-device.md)
