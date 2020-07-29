---
title: 设置 Azure 门户首选项 | Microsoft Docs
description: 你可以根据自己的偏好更改 Azure 门户的默认设置。 设置包括非活动会话超时、默认视图、菜单模式、对比度、主题、通知，以及语言和区域格式
services: azure-portal
keywords: 设置, 超时, 语言, 区域
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: how-to
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: d4c675ab18a7a9231229ce0c7de9a7450dc1baa8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763952"
---
# <a name="set-your-azure-portal-preferences"></a>设置 Azure 门户首选项

你可以根据自己的偏好更改 Azure 门户的默认设置。 可以更改下面列出的每项设置：

* [非活动会话超时](#change-the-idle-duration-for-inactive-sign-out)
* [默认视图](#choose-your-default-view)
* [门户菜单模式](#choose-a-portal-menu-mode)
* [颜色和高对比度主题](#choose-a-theme)
* [弹出式通知](#enable-or-disable-pop-up-notifications)
* [语言和区域格式](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>更改常规门户设置

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在全局页头中选择“设置”。 

    ![显示全局页头图标的屏幕截图，其中突出显示了“设置”](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>更改非活动注销的空闲持续时间

如果你忘记了保护工作站，非活动超时设置可帮助防范资源受到未经授权的访问。 空闲一段时间后，将自动注销你的 Azure 门户会话。

选择“不活动时将我注销”下面的下拉列表。  选择在空闲多长时间后注销你的 Azure 门户会话。

   ![显示门户设置的屏幕截图，其中突出显示了非活动超时设置](./media/set-preferences/inactive-signout-user.png)

更改将自动保存。 空闲持续时间达到设置的值后，将注销你的 Azure 门户会话。

管理员还可以在目录级别指定此设置，以强制实施最长空闲时间。 如果管理员指定了目录级别的超时设置，你仍可以设置自己的非活动注销持续时间。 选择小于目录级设置值的时间设置。

如果管理员启用了非活动超时策略，请选中“替代目录非活动超时策略”复选框。  设置小于策略设置的时间间隔。

   ![显示门户设置的屏幕截图，其中突出显示了“替代目录非活动超时策略”设置](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> 如果你是管理员，想要为 Azure 门户的所有用户强制实施非活动超时设置，请参阅[为 Azure 门户的用户设置目录级非活动超时](admin-timeout.md)
>

### <a name="choose-your-default-view"></a>选择默认视图 

可以更改在登录到 Azure 门户时默认打开的页面。

   ![显示 Azure 门户设置的屏幕截图，其中突出显示了默认视图](./media/set-preferences/default-view.png)

默认视图设置控制登录时显示的 Azure 门户视图。 可以选择默认打开 Azure 主页或仪表板视图。

* 无法自定义**主页**。  主页中显示了热门 Azure 服务的快捷方式，并列出了最近用过的资源。 我们还会提供 Microsoft Learn 和 Azure 路线图等资源的有用链接。
* 可以自定义仪表板，以创建专为你设计的工作区。 例如，可以生成一个以项目、任务或角色为中心的仪表板。 如果选择“仪表板”，则默认视图将切换到最近使用的仪表板。 

### <a name="choose-a-portal-menu-mode"></a>选择门户菜单模式

门户菜单的默认模式控制门户菜单在页面上占用的空间量。

* 当门户菜单处于**飞**入模式时，它会一直处于隐藏状态，直到您需要它。 选择菜单图标可以打开或关闭菜单。
* 如果为门户菜单选择**停靠**模式，则它始终可见。 可以折叠菜单以提供更多的工作空间。 

### <a name="choose-a-theme"></a>选择主题

选择的主题将影响 Azure 门户中显示的背景和字体颜色。 可以从四个预设颜色主题中选择一个。 选择每个缩略图找到最适合自己的主题。

   ![显示 Azure 门户设置的屏幕截图，其中突出显示了“主题”](./media/set-preferences/theme.png)

可以改为选择一个高对比度主题。 通过 "高对比度" 设置，可以更轻松地阅读具有视觉障碍的人士的 Azure 门户，并覆盖所有其他主题选择。 有关详细信息，请参阅[启用高对比度或更改主题](azure-portal-change-theme-high-contrast.md)。

### <a name="enable-or-disable-pop-up-notifications"></a>启用或禁用弹出式通知

通知是与当前会话相关的系统消息。 例如，它们提供如下所述的信息：当前的信用余额、刚刚创建的资源何时可用，或者请求确认上一个操作。 启用弹出式通知后，消息会短暂地显示在屏幕的右上角。 

若要启用或禁用弹出式通知，请选中或取消选中“启用弹出式通知”复选框。 

   ![显示 Azure 门户设置的屏幕截图，其中突出显示了弹出式通知](./media/set-preferences/popup-notifications.png)

若要阅读在当前会话中收到的所有通知，请从全局页头中选择“通知”。 

   ![显示 Azure 门户全局页头的屏幕截图，其中显示突出了“通知”](./media/set-preferences/read-notifications.png)

若要阅读以前会话中的通知，请查看活动日志中的事件。 有关详细信息，请参阅[查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view)。

### <a name="settings-under-useful-links"></a>有用链接下的设置

如果你对 Azure 门户设置做了更改，但想要放弃这些更改，请选择“还原默认设置”。**** 对门户设置所做的所有更改将会丢失。 此选项不影响仪表板自定义。

有关“导出所有设置”或“删除所有设置和专用仪表板”的详细信息，请参阅[导出或删除用户设置](azure-portal-export-delete-settings.md)。 

## <a name="change-language-and-regional-settings"></a>更改语言和区域设置

有两项设置用于控制文本在 Azure 门户中的显示方式。 “语言”设置控制文本在 Azure 门户中的显示语言。  “区域格式”控制日期、时间、数字和货币的显示方式。 

若要更改 Azure 门户中使用的语言，请使用下拉列表从可用语言列表中进行选择。

选择后，区域格式将会更改，仅显示所选语言的区域选项。 若要更改自动选择的设置，请使用下拉列表选择所需的区域格式。

例如，如果选择“英语”作为语言，然后选择“美国”作为区域格式，将以美元显示货币。 如果选择“英语”作为语言，然后选择“欧洲”作为区域格式，则以欧元显示货币。

选择“应用”以更新语言和区域格式设置。 

   ![显示语言和区域格式设置的屏幕截图](./media/set-preferences/language.png)

>[!NOTE]
>这些语言和区域设置只影响 Azure 门户。 在新选项卡或窗口中打开的文档链接将使用浏览器的语言设置来确定要显示的语言。
>

## <a name="next-steps"></a>后续步骤

* [创建和共享自定义仪表板](azure-portal-dashboards.md)
* [Azure 门户“操作说明”视频系列](azure-portal-video-series.md)
