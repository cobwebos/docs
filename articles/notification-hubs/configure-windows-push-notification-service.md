---
title: 在 Azure 通知中心中配置 Windows 推送通知服务 |Microsoft Docs
description: 了解如何配置 Azure 通知中心的 Windows 推送通知服务设置。
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: c3e3f1e7df5c90c690756375ff1e1b0350c72714
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488182"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>在 Azure 门户中配置通知中心的 Windows 推送通知服务 (WNS) 设置
本文介绍如何使用 Azure 门户配置 Azure 通知中心的 Windows 通知服务 (WNS) 设置。  

## <a name="prerequisites"></a>必备组件
如果你尚未创建通知中心，现在请创建一个。 有关详细信息，请参阅[在 Azure 门户中创建 Azure 通知中心](create-notification-hub-portal.md)。 

## <a name="configure-windows-push-notification-service-wns"></a>配置 Windows 推送通知服务 (WNS)

以下过程提供配置通知中心的 Windows 推送通知服务 (WNS) 设置的步骤： 

1. 在 Azure 门户中，在**通知中心**页上，选择**Windows (WNS)** 在左侧菜单中。
2. 输入“包 SID”和“安全密钥”的值。
3. 选择“保存”。

   ![显示“包 SID”和“安全密钥”框的屏幕截图](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>后续步骤
有关通过使用 Azure 通知中心和 Windows 推送通知服务 (WNS) 将通知推送到通用 Windows 平台应用程序的分步说明的教程，请参阅[使用 Azure 将通知发送到 UWP 应用通知中心](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)。


