---
title: 在 Azure 通知中心配置 Microsoft 推送通知服务 |微软文档
description: 了解如何为 Azure 通知中心配置 Microsoft 推送通知服务设置。
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
ms.openlocfilehash: 99f29e7910fe6070c6202f6a936173455f979732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127325"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>在 Azure 门户中配置 Microsoft 推送通知服务 （MPNS） 设置

本文介绍如何使用 Azure 门户为 Azure 通知中心配置 Microsoft 推送通知服务 (MPNS) 设置。 

## <a name="prerequisites"></a>先决条件
如果尚未创建通知中心，请立即创建。 有关详细信息，请参阅[在 Azure 门户中创建 Azure 通知中心](create-notification-hub-portal.md)。 

## <a name="configure-microsoft-push-notification-service-mpns"></a>配置 Microsoft 推送通知服务 (MPNS)

以下过程提供的步骤演示了如何为通知中心配置 Microsoft 推送通知服务 (MPNS) 设置： 

1. 在 Azure 门户的“通知中心”页上，在左侧菜单中选择“Windows Phone (MPNS)”。********
1. 启用未经身份验证或经过身份验证的推送通知：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 要启用未身份验证的推送通知，请选择 **"启用未身份验证的推送** > **保存**"。

      ![显示如何启用未经身份验证的推送的屏幕截图](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b.保留“数据库类型”设置，即设置为“共享”。 要启用经过身份验证的推送通知：
      * 在工具栏上选择“上传证书”****。
      * 选择文件图标，然后选择证书文件。
      * 输入证书的密码。
      * 选择“确定”。
      * 在“Windows Phone (MPNS)”页上选择“保存”。********

## <a name="next-steps"></a>后续步骤
如需通过教程的分步说明来了解如何使用 Azure 通知中心和 Microsoft 推送通知服务 (MPNS) 将通知推送到 Windows Phone 设备，请参阅[使用通知中心将通知推送到 Windows Phone 应用](notification-hubs-windows-mobile-push-notifications-mpns.md)。

