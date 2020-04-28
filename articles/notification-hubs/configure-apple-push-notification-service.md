---
title: 在 Azure 通知中心中配置 Apple Push Notification 服务 |Microsoft Docs
description: 了解如何为 Azure 通知中心配置 Apple Push Notification 服务 (APNS) 设置。
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
ms.openlocfilehash: eb1122ba3de0002507589d3e607d1e39d905c308
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80127514"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>为通知中心配置 Apple Push Notification 服务设置 Azure 门户

本文介绍如何使用 Azure 门户为 Azure 通知中心配置 Apple Push Notification 服务 (APNS) 设置。 

## <a name="prerequisites"></a>先决条件
如果你尚未创建通知中心，现在请创建一个。 有关详细信息，请参阅[在 Azure 门户中创建 Azure 通知中心](create-notification-hub-portal.md)。 

## <a name="configure-apple-push-notification-service"></a>配置 Apple Push Notification 服务

以下过程提供的步骤演示了如何为通知中心配置 Apple Push Notification 服务 (APNS) 设置：

1. 在 Azure 门户的“通知中心”页上，在左侧菜单中选择“Apple (APNS)”。  

1. 对于“身份验证模式”，请选择“证书”或“令牌”。   

   a. 如果选择“证书”： 
   * 选择“文件”图标，然后选择要上传的“.p12”文件。 
   * 输入密码。
   * 选择“沙盒”  模式。 或者，若要将推送通知发送给从应用商店中购买了你的应用的用户，请选择“生产”模式。 

     ![Azure 门户中 APNS 证书配置的屏幕截图](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. 如果选择“令牌”： 

   * 输入“密钥 ID”、“绑定 ID”、“团队 ID”和“令牌”的值     。
   * 选择“沙盒”  模式。 或者，若要将推送通知发送给从应用商店中购买了你的应用的用户，请选择“生产”模式。 

     ![Azure 门户中 APNS 令牌配置的屏幕截图](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>后续步骤
如需通过教程的 分步说明来了解如何将通知推送到 iOS 设备，请参阅以下文章：[使用通知中心和 APNS 将通知推送到 iOS 设备](notification-hubs-ios-apple-push-notification-apns-get-started.md)
