---
title: 管理 Azure 门户设置和首选项 | Microsoft Docs
description: 你可以根据自己的偏好更改 Azure 门户的默认设置。 设置包括非活动会话超时、默认视图、菜单模式、对比度、主题、通知，以及语言和区域格式
services: azure-portal
keywords: 设置, 超时, 语言, 区域
author: mgblythe
ms.author: mblythe
ms.date: 08/05/2020
ms.topic: how-to
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 20ed84a87486f1095a90e012368b1f56d6426c8e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88205723"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>管理 Azure 门户设置和首选项

你可以根据自己的偏好更改 Azure 门户的默认设置。 全局页眉中的“设置”菜单中提供了大多数设置。

![显示全局页头图标的屏幕截图，其中突出显示了“设置”](./media/set-preferences/header-settings.png)


## <a name="choose-your-default-subscription"></a>选择默认订阅

可以更改在登录到 Azure 门户时默认打开的订阅。 如果你有所使用的主订阅，但偶尔也会使用其他订阅，此功能会很有用。 

:::image type="content" source="media/set-preferences/filter-subscription-default-view.png" alt-text="按订阅筛选资源列表。":::

1. 在全局页眉中选择“目录和订阅筛选器”图标。

1. 选择要在启动门户时用作默认订阅的订阅。 

    :::image type="content" source="media/set-preferences/default-directory-subscription-filter.png" alt-text="按订阅筛选资源列表。"::: 


## <a name="choose-your-default-view"></a>选择默认视图 

可以更改在登录到 Azure 门户时默认打开的页面。

![显示 Azure 门户设置的屏幕截图，其中突出显示了默认视图](./media/set-preferences/default-view.png)

- 无法自定义主页。  主页将显示热门 Azure 服务的快捷方式，并列出你最近使用的资源。 我们还会提供 Microsoft Learn 和 Azure 路线图等资源的有用链接。

- 可以自定义仪表板，以创建专为你设计的工作区。 例如，可以生成一个以项目、任务或角色为中心的仪表板。 如果选择“仪表板”，则默认视图将切换到最近使用的仪表板。**** 有关详细信息，请参阅[在 Azure 门户中创建和共享仪表板](azure-portal-dashboards.md)。

## <a name="choose-a-portal-menu-mode"></a>选择门户菜单模式

门户菜单的默认模式控制门户菜单在页面上占用的空间量。

![显示 Azure 门户设置的屏幕截图，其中突出显示了“主题”](./media/set-preferences/menu-mode.png)

- 门户菜单处于“浮出控件”模式时，它会被隐藏，直到你需要使用它。 选择菜单图标可以打开或关闭菜单。

- 如果为门户菜单选择“停靠”模式，它就会始终可见。 可以折叠菜单以提供更多的工作空间。

## <a name="choose-a-theme-or-enable-high-contrast"></a>选择主题或启用高对比度

选择的主题将影响 Azure 门户中显示的背景和字体颜色。 可以从四个预设颜色主题中选择一个。 选择每个缩略图找到最适合自己的主题。

或者可以选择高对比度主题之一。 高对比度主题可让有视觉障碍的用户在使用 Azure 门户时更容易阅读。这些主题会替代所有其他主题选择。

![显示 Azure 门户设置的屏幕截图，其中突出显示了“主题”](./media/set-preferences/theme.png)

## <a name="enable-or-disable-pop-up-notifications"></a>启用或禁用弹出式通知

通知是与当前会话相关的系统消息。 例如，它们提供如下所述的信息：当前的信用余额、刚刚创建的资源何时可用，或者请求确认上一个操作。 启用弹出式通知后，消息会短暂地显示在屏幕的右上角。 

若要启用或禁用弹出式通知，请选中或清除“启用弹出式通知”。

![显示 Azure 门户设置的屏幕截图，其中突出显示了弹出式通知](./media/set-preferences/popup-notifications.png)

若要阅读在当前会话中收到的所有通知，请从全局页头中选择“通知”。****

![显示 Azure 门户全局页头的屏幕截图，其中显示突出了“通知”](./media/set-preferences/read-notifications.png)

若要阅读以前会话中的通知，请查看活动日志中的事件。 有关详细信息，请参阅[查看活动日志](../azure-monitor/platform/activity-log.md#view-the-activity-log)。 

## <a name="change-the-inactivity-timeout-setting"></a>更改非活动状态超时设置

如果你忘记了保护工作站，非活动状态超时设置有助于保护资源免受未经授权的访问。 空闲一段时间后，将自动注销你的 Azure 门户会话。 作为个人，你可以更改自己的超时设置。 如果你是管理员，可在目录级别为目录中所有用户完成这项设置。

### <a name="change-your-individual-timeout-setting-user"></a>更改个人的超时设置（用户）

选择“不活动时将我注销”下面的下拉列表。**** 选择在空闲多长时间后注销你的 Azure 门户会话。

![显示门户设置的屏幕截图，其中突出显示了非活动超时设置](./media/set-preferences/inactive-signout-user.png)

更改将自动保存。 空闲持续时间达到设置的值后，将注销你的 Azure 门户会话。

如果你的管理员已经启用了非活动状态超时策略，你仍然可以设置自己的超时设置，只是它必须小于目录级别设置。 选中“替代目录非活动状态超时策略”，然后设置时间间隔。

![显示门户设置的屏幕截图，其中突出显示了“替代目录非活动超时策略”设置](./media/set-preferences/inactive-signout-override.png)

### <a name="change-the-directory-timeout-setting-admin"></a>更改目录超时设置（管理员）

具有[全局管理员角色](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)的管理员可以在会话注销之前强制执行最长空闲时间。非活动超时设置在目录级别应用。 设置对新会话生效。 它不会立即应用于任何已登录的用户。 有关目录的详细信息，请参阅 [Active Directory 域服务概述](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。

如果你是全局管理员，并且需要为 Azure 门户的所有用户强制执行空闲超时设置，请执行以下步骤：

1. 选择链接文本“配置目录级别超时”****。

    ![显示门户设置的屏幕截图，其中突出显示了链接文本](./media/set-preferences/settings-admin.png)

1. 在“配置目录级非活动超时”**** 页上，选择“为 Azure 门户启用目录级别的空闲超时”**** 以启用设置。

1. 接下来，输入**小时数**和**分钟数**，以便设置在会话自动注销之前用户可处于空闲状态的最长时间。

1. 选择“应用”。

    ![屏幕截图，显示用于设置目录级别非活动超时的页面](./media/set-preferences/configure.png)

若要确认是否正确设置了非活动超时策略，请从全局页眉中选择“通知”。**** 验证是否列出了成功通知。

![屏幕截图，显示用于目录级别非活动超时的成功通知消息](./media/set-preferences/confirmation.png)

## <a name="restore-default-settings"></a>还原默认设置

如果你对 Azure 门户设置做了更改，但想要放弃这些更改，请选择“还原默认设置”。 对门户设置所做的所有更改将会丢失。 此选项不影响仪表板自定义。

![显示如何还原默认设置的屏幕截图](./media/set-preferences/useful-links-restore-defaults.png)

## <a name="export-user-settings"></a>导出用户设置

有关自定义设置的信息存储在 Azure 中。 可以导出以下用户数据：

* Azure 门户中的专用仪表板
* 用户设置，例如最喜欢的订阅或目录，以及上次登录的目录
* 主题和其他自定义门户设置

如果计划删除设置，建议先导出并检查这些设置。 重新生成仪表板或重新进行设置可能会非常耗时。

若要导出门户设置，请选择“导出所有设置”。

![显示如何导出设置的屏幕截图](./media/set-preferences/useful-links-export-settings.png)

导出设置时会创建一个 .json 文件，其中包含你的用户设置（如颜色主题、收藏夹和专用仪表板）。 由于用户设置的动态性质和数据损坏风险，因此不能从 .json** 文件导入设置。

## <a name="delete-user-settings-and-dashboards"></a>删除用户设置和仪表板

有关自定义设置的信息存储在 Azure 中。 可以删除以下用户数据：

* Azure 门户中的专用仪表板
* 用户设置，例如最喜欢的订阅或目录，以及上次登录的目录
* 主题和其他自定义门户设置

在删除设置之前，建议先导出并检查设置。 重新生成仪表板或重做自定义设置可能非常耗时。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

若要删除门户设置，请选择“删除所有设置和专用仪表板”。

![显示如何删除设置的屏幕截图](./media/set-preferences/useful-links-delete-settings.png)

## <a name="change-language-and-regional-settings"></a>更改语言和区域设置

有两项设置控制 Azure 门户中文本的显示方式： 
- “语言”设置控制文本在 Azure 门户中的显示语言。**** 

- “区域格式”控制日期、时间、数字和货币的显示方式。****

若要更改 Azure 门户中使用的语言，请使用下拉列表从可用语言列表中进行选择。

选择后，区域格式将会更改，仅显示所选语言的区域选项。 若要更改自动选择的设置，请使用下拉列表选择所需的区域格式。

例如，如果选择“英语”作为语言，然后选择“美国”作为区域格式，将以美元显示货币。 如果选择“英语”作为语言，然后选择“欧洲”作为区域格式，则以欧元显示货币。

选择“应用”以更新语言和区域格式设置。****

   ![显示语言和区域格式设置的屏幕截图](./media/set-preferences/language.png)

>[!NOTE]
>这些语言和区域设置只影响 Azure 门户。 在新选项卡或窗口中打开的文档链接会使用浏览器的语言设置来确定要显示的语言。
>

## <a name="next-steps"></a>后续步骤

- [Azure 门户中的键盘快捷方式](azure-portal-keyboard-shortcuts.md)
- [支持的浏览器和设备](azure-portal-supported-browsers-devices.md)
- [添加、删除和重新排列收藏夹](azure-portal-add-remove-sort-favorites.md)
- [创建并共享自定义仪表板](azure-portal-dashboards.md)
- [Azure 门户“操作说明”视频系列](azure-portal-video-series.md)
