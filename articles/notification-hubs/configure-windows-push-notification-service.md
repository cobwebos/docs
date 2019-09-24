---
title: 在 Azure 通知中心中配置 Windows 推送通知服务 |Microsoft Docs
description: 了解如何为 Azure 通知中心配置 Windows 推送通知服务设置。
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: a7f7734d97cd67c133ff0cedc3ef2376967bcdf4
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212414"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>在 Azure 门户中为通知中心配置 Windows 推送通知服务 (WNS) 设置
本文介绍如何使用 Azure 门户为 Azure 通知中心配置 Windows 通知服务 (WNS) 设置。  

## <a name="prerequisites"></a>先决条件
如果尚未创建通知中心，请立即创建。 有关详细信息，请参阅[在 Azure 门户中创建 Azure 通知中心](create-notification-hub-portal.md)。 

## <a name="configure-windows-push-notification-service-wns"></a>配置 Windows 推送通知服务 (WNS)

以下过程提供的步骤演示了如何为通知中心配置 Windows 推送通知服务 (WNS) 设置： 

1. 在 Azure 门户的“通知中心”页上，在左侧菜单中选择“Windows (WNS)”。
2. 输入“包 SID”和“安全密钥”的值。
3. 选择“保存”。

   ![显示“包 SID”框和“安全密钥”框的屏幕截图](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>后续步骤
如需通过教程的分步说明来了解如何使用 Azure 通知中心和 Windows 推送通知服务 (WNS) 将通知推送到通用 Windows 平台应用程序，请参阅[使用 Azure 通知中心将通知发送到 UWP 应用](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)。


