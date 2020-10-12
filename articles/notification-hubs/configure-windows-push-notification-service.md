---
title: 在 Azure 通知中心配置 Windows 推送通知服务 | Microsoft Docs
description: 了解如何为 Azure 通知中心配置 Windows 推送通知服务设置。
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 62d73a23fff3bd15d873a579d451b2f91509b7fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87758724"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>在 Azure 门户中配置 Windows 推送通知服务设置

本文介绍如何使用 Azure 门户为 Azure 通知中心配置 Windows 通知服务 (WNS) 设置。  

## <a name="prerequisites"></a>先决条件

如果你尚未创建通知中心，现在请创建一个。 有关详细信息，请参阅[在 Azure 门户中创建 Azure 通知中心](create-notification-hub-portal.md)。

## <a name="configure-windows-push-notification-service-wns"></a>配置 Windows 推送通知服务 (WNS)

以下过程介绍了如何为通知中心配置 Windows 推送通知服务 (WNS) 设置：

1. 在 Azure 门户的“通知中心”页上，在左侧菜单中选择“Windows (WNS)”。  
2. 输入“包 SID”和“安全密钥”的值。  
3. 选择“保存”  。

   ![显示“包 SID”和“安全密钥”框的屏幕截图](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>后续步骤

如需通过教程的分步说明来了解如何使用 Azure 通知中心和 Windows 推送通知服务 (WNS) 将推送通知发送到通用 Windows 平台应用程序，请参阅[使用 Azure 通知中心将通知发送到 UWP 应用](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)。
