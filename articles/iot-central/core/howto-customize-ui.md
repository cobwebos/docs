---
title: 自定义 Azure IoT Central UI | Microsoft Docs
description: 如何自定义 Azure IoT Central 应用程序的主题和帮助链接
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 660f6dfae23331455408ae7127274550a3a4cc3a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90019829"
---
# <a name="customize-the-azure-iot-central-ui"></a>自定义 Azure IoT Central UI

本文介绍管理员如何通过应用自定义主题，并将帮助链接修改为指向自己的自定义帮助资源，来自定义应用程序的 UI。 

以下屏幕截图显示了使用标准主题的页面：

![标准 IoT Central 主题](./media/howto-customize-ui/standard-ui.png)

以下屏幕截图显示了使用自定义主题的页面，其中突出显示了自定义 UI 元素：

![自定义 IoT Central 主题](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>创建主题

若要创建自定义主题，请在“管理”部分导航到“自定义应用程序”页：  

![IoT Central 主题](./media/howto-customize-ui/themes.png)

在此页上，可以自定义应用程序的以下方面：

### <a name="application-logo"></a>应用程序徽标

大小不超过 1 MB 且带有透明背景的 PNG 图像。 此徽标显示在 IoT Central 应用程序标题栏的左侧。

如果徽标图像包含应用程序的名称，你可以隐藏应用程序名称文本。 有关详细信息，请参阅[管理应用程序](howto-administer.md#change-application-name-and-url)。

### <a name="browser-icon-favicon"></a>浏览器图标（网站图标）

大小不超过 32 x 32 像素且带有透明背景的 PNG 图像。 Web 浏览器可以在地址栏、历史记录、书签和浏览器标签页中使用此图像。

### <a name="browser-colors"></a>浏览器颜色

可以更改页头的颜色，以及用于突出按钮和其他强调内容的颜色。 请使用 `##ff6347` 格式的六个字符十六进制颜色值。 有关**十六进制值**颜色表示法的详细信息，请参阅 [HTML 颜色](https://www.w3schools.com/html/html_colors.asp)。

> [!NOTE]
> 始终可以还原为“自定义应用程序”页上的默认选项。 

### <a name="changes-for-operators"></a>面向操作员的更改

如果管理员创建了自定义主题，则应用程序的操作员和其他用户不再可以在“设置”中选择主题。 

## <a name="replace-help-links"></a>替换帮助链接

若要为操作员和其他用户提供自定义帮助信息，可以修改应用程序“帮助”菜单中的链接。 

若要修改帮助链接，请在“管理”部分导航到“自定义帮助”页：  

![自定义 IoT Central 帮助链接](./media/howto-customize-ui/help-links.png)

还可以将新条目添加到帮助菜单，以及删除默认条目：

![自定义 IoT Central 帮助](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> 始终可以还原为“自定义帮助”页上的默认帮助链接。 

## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中自定义 UI 后，建议接下来学习：

- [管理应用程序](./howto-administer.md)
- [向仪表板添加磁贴](howto-add-tiles-to-your-dashboard.md)