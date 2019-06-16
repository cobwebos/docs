---
title: include 文件
description: include 文件
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 244a4ebe20863945bfc3b6236e70e786387c8909
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116691"
---
1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在左侧菜单上选择“所有服务”  ，然后在“移动”部分中选择“通知中心”。   选择服务名称旁边的星形图标，以便将服务添加到左侧菜单上的“收藏夹”部分。  在将“通知中心”  添加到“收藏夹”  之后，在左侧菜单上选择它。

      ![Azure 门户 - 选择“通知中心”](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. 在“通知中心”  页面上，在工具栏上选择“添加”  。

      ![通知中心 -“添加”工具栏按钮](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. 在“通知中心”  页面上，执行以下步骤：

    1. 将一个名称输入“通知中心”。   

    1. 在“创建新的命名空间”中输入名称。  命名空间包含一个或多个中心。

    1. 从“位置”下拉列表框中选择值。  此值指定要在其中创建通知中心的位置。

    1. 在“资源组”中选择现有的资源组，或者为新资源组创建一个名称。 

    1. 选择“创建”  。

        ![Azure 门户 - 设置通知中心属性](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. 选择“通知”（钟形图标），然后选择“转到资源”   。 还可以刷新“通知中心”  页面上的列表，然后选择通知中心。

      ![Azure 门户 - 通知 -> 转到资源](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. 从列表中选择“访问策略”  。 记下向你提供的两个连接字符串。 稍后在处理推送通知时需要它们。

      >[!IMPORTANT]
      >请勿  在应用程序中使用 **DefaultFullSharedAccessSignature** 策略。 这只能在后端使用。
      >

      ![Azure 门户 - 通知中心连接字符串](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
