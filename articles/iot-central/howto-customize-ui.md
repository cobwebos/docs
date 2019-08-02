---
title: 自定义 Azure IoT Central UI |Microsoft Docs
description: 如何自定义 Azure IoT 中心应用程序的主题和帮助链接
author: dominicbetts
ms.author: dobett
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: cef0014fb47aeddcad785fb3f938ab928964a942
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725691"
---
# <a name="customize-the-azure-iot-central-ui"></a>自定义 Azure IoT Central UI 

本文介绍如何以管理员身份通过应用自定义主题和修改帮助链接来自定义应用程序的 UI, 以指向你自己的自定义帮助资源。 

以下屏幕截图显示了使用标准主题的页面:

![标准 IoT Central 主题](./media/howto-customize-ui/standard-ui.png)

以下屏幕截图显示了使用自定义屏幕截图的页面, 其中突出显示了自定义 UI 元素:

![自定义 IoT Central 主题](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>创建主题

若要创建自定义主题, 请导航到 "**管理**" 部分中的 "**自定义应用程序**" 页:

![IoT Central 主题](./media/howto-customize-ui/themes.png)

在此页上, 你可以自定义应用程序的以下方面:

### <a name="application-logo"></a>应用程序徽标

不超过 1 MB 且带有透明背景的 PNG 图像。 此徽标显示在 IoT Central 应用程序标题栏的左侧。

如果你的徽标图像包含你的应用程序的名称, 你可以隐藏应用程序名称文本。 有关详细信息, 请参阅[管理应用程序](./howto-administer.md#change-application-name-and-url)。

### <a name="browser-icon-favicon"></a>浏览器图标 (favicon)

不超过 32 x 32 像素且带有透明背景的 PNG 图像。 Web 浏览器可以在地址栏、"历史记录"、"书签" 和 "浏览器" 选项卡中使用此图像。

### <a name="browser-colors"></a>浏览器颜色

您可以更改页眉的颜色以及用于重音按钮和其他突出显示的颜色。 使用格式`##ff6347`的六个字符十六进制颜色值。 有关**十六进制值**颜色表示法的详细信息, 请参阅[HTML 颜色](https://www.w3schools.com/html/html_colors.asp)。

> [!NOTE]
> 始终可以还原到 "**自定义应用程序**" 页上的默认选项。

### <a name="changes-for-operators"></a>运算符的更改

如果管理员创建自定义主题, 则应用程序的操作员和其他用户将无法再在 "**设置**" 中选择主题。

## <a name="replace-help-links"></a>替换帮助链接

若要为操作员和其他用户提供自定义帮助信息, 可以修改应用程序**帮助**菜单上的链接。

若要修改帮助链接, 请导航到 "**管理**" 部分中的 "**自定义帮助**" 页:

![自定义 IoT Central 帮助链接](./media/howto-customize-ui/help-links.png)

你还可以将新条目添加到 "帮助" 菜单并删除默认条目:

![自定义 IoT Central 帮助](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> 始终可以还原到 "**自定义帮助**" 页上的默认 "帮助" 链接。

## <a name="next-steps"></a>后续步骤

现在, 你已了解如何在 IoT Central 应用程序中自定义 UI, 下面是一些建议的后续步骤:

- [管理应用程序](./howto-administer.md)
- [配置应用程序仪表板](./howto-configure-homepage.md)