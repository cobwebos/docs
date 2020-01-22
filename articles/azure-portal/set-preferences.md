---
title: 设置 Azure 门户首选项 |Microsoft Docs
description: 您可以更改 Azure 门户默认设置，以满足您的偏好。 设置包括非活动会话超时、默认视图、菜单模式、对比度、主题、通知和语言和区域格式
services: azure-portal
keywords: 设置，超时，语言，区域
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 7bcfdeec832b14eb53c0dab6cb2f53970d85c804
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310673"
---
# <a name="set-your-azure-portal-preferences"></a>设置 Azure 门户首选项

您可以更改 Azure 门户的默认设置，以满足您的偏好。 下面列出的每个设置可以更改：

* [非活动会话超时](#change-the-idle-duration-for-inactive-sign-out)
* [默认视图](#choose-your-default-view)
* [门户菜单模式](#choose-a-portal-menu-mode)
* [颜色和高对比度主题](#choose-a-theme)
* [弹出式通知](#enable-or-disable-pop-up-notifications)
* [语言和区域格式](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>更改常规门户设置

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 从全局页眉中选择 "**设置**"。

    ![显示设置突出显示的全局页眉图标的屏幕截图](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>更改非活动注销的空闲持续时间

如果你忘记保护工作站，则 "非活动超时" 设置有助于防止对资源进行未经授权的访问。 闲置一段时间后，会自动从 Azure 门户会话中注销。

选择 "不**活动时注销**" 下的下拉箭头。 如果空闲，请选择 Azure 门户会话注销的持续时间。

   ![显示突出显示了非活动超时设置的门户设置的屏幕截图](./media/set-preferences/inactive-signout-user.png)

更改将自动保存。 如果你处于空闲状态，你的 Azure 门户会话将在你设置的持续时间后注销。

此设置还可以由目录级别的管理员进行，以强制使用最长空闲时间。 如果管理员已进行目录级别的超时设置，你仍可以设置自己的非活动注销持续时间。 选择小于在目录级别设置的时间设置。

如果管理员已启用非活动超时策略，请选中 "**替代目录非活动超时策略**" 复选框。 设置一个小于策略设置的时间间隔。

   ![显示门户设置的屏幕截图，其中突出显示了 "目录非活动超时" 策略设置](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> 如果你是管理员，并且想要为 Azure 门户的所有用户强制使用非活动超时设置，请参阅[为 Azure 门户的用户设置目录级别的非活动超时](admin-timeout.md)
>

### <a name="choose-your-default-view"></a>选择默认视图 

登录到 Azure 门户时，可以更改默认打开的页面。

   ![显示突出显示默认视图 Azure 门户设置的屏幕截图](./media/set-preferences/default-view.png)

默认的 "视图" 设置控制登录时显示的 Azure 门户视图。 默认情况下，你可以选择打开 Azure Home 或仪表板视图。

* 无法自定义**Home** 。  它显示了常用 Azure 服务的快捷方式，并列出了最近使用过的资源。 我们还会向你介绍 Microsoft Learn 和 Azure 路线图等资源的有用链接。
* 可以自定义仪表板来创建只为你设计的工作区。 例如，你可以生成一个以项目、任务或角色为中心的仪表板。 如果选择 "**仪表板**"，默认视图将使用最近使用的仪表板。

### <a name="choose-a-portal-menu-mode"></a>选择门户菜单模式

门户菜单的默认模式控制门户菜单在页面上所占的空间量。

* 当门户菜单处于**飞**入模式时，它会一直处于隐藏状态，直到您需要它。 选择菜单图标以打开或关闭菜单。
* 如果为门户菜单选择**停靠**模式，则它始终可见。 可以折叠菜单以提供更多的工作空间。 

### <a name="choose-a-theme"></a>“选择主题”

你选择的主题将影响 Azure 门户中显示的背景和字体颜色。 可以从四个预设颜色主题之一中进行选择。 选择每个缩略图以查找最适合您的主题。

   ![显示突出显示主题 Azure 门户设置的屏幕截图](./media/set-preferences/theme.png)

您可以改为选择一个高对比度主题。 "高对比度" 设置使 Azure 门户更易于阅读视觉障碍的用户，并覆盖所有其他主题选择。 有关详细信息，请参阅[打开高对比度或更改主题](azure-portal-change-theme-high-contrast.md)。

### <a name="enable-or-disable-pop-up-notifications"></a>启用或禁用弹出通知

通知是与当前会话相关的系统消息。 例如，当您刚刚创建的资源可用时，它们提供了当前信用额度等信息，或确认上一个操作。 打开弹出通知时，消息会短暂显示在屏幕的右上角。 

若要启用或禁用弹出通知，请选中或取消选中 "**启用弹出式通知**" 复选框。

   ![显示突出显示了弹出式通知 Azure 门户设置的屏幕截图](./media/set-preferences/popup-notifications.png)

若要读取当前会话期间收到的所有通知，请从全局标头中选择 "**通知**"。

   ![显示突出显示了通知 Azure 门户全局标头的屏幕截图](./media/set-preferences/read-notifications.png)

如果要读取以前会话的通知，请在活动日志中查找事件。 若要了解详细信息，请参阅[查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view)。

### <a name="settings-under-useful-links"></a>有用链接下的设置

如果已对 Azure 门户设置进行了更改，但想要将其丢弃，请选择 "**还原默认设置**"。 对门户设置所做的任何更改都将丢失。 此选项不影响仪表板自定义项。

有关**导出所有设置**或**删除所有设置和私有仪表板**的详细信息，请参阅[导出或删除用户设置](azure-portal-export-delete-settings.md)。

## <a name="change-language-and-regional-settings"></a>更改语言和区域设置

可以通过两个设置来控制 Azure 门户中的文本的显示方式。 "**语言**" 设置控制在 Azure 门户中看到的文本语言。 **区域格式**控制日期、时间、数字和货币的显示方式。

若要更改 Azure 门户中使用的语言，请使用下拉列表从可用语言列表中进行选择。

区域格式选择将更改为仅显示所选语言的区域选项。 若要更改该自动选择，请使用下拉箭头选择所需的区域格式。

例如，如果选择 "英语" 作为语言，然后选择 "美国" 作为 "区域格式"，则将以美元显示货币。 如果选择 "英语" 作为语言，然后选择 "欧洲" 作为区域格式，则货币以欧元显示。

选择 "**应用**"，更新语言和区域格式设置。

   ![显示语言和区域格式设置的屏幕截图](./media/set-preferences/language.png)

>[!NOTE]
>这些语言和区域设置只影响 Azure 门户。 在新的选项卡或窗口中打开的文档链接将使用浏览器的语言设置来确定要显示的语言。
>

## <a name="next-steps"></a>后续步骤

* [创建和共享自定义仪表板](azure-portal-dashboards.md)
* [Azure 门户操作方法视频系列](azure-portal-video-series.md)
