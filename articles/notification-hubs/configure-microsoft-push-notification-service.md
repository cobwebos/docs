---
title: 在 Azure 通知中心中配置 Microsoft 推送通知服务 |Microsoft Docs
description: 了解如何配置 Azure 通知中心的 Microsoft 推送通知服务设置。
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 1c76b44438e6527439d0a370c92f4120424b8da5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488204"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>在 Azure 门户中配置通知中心的 Microsoft 推送通知服务 (MPNS) 设置
本文介绍如何使用 Azure 门户配置 Azure 通知中心的 Microsoft 推送通知服务 (MPNS) 设置。 

## <a name="prerequisites"></a>必备组件
如果你尚未创建通知中心，现在请创建一个。 有关详细信息，请参阅[在 Azure 门户中创建 Azure 通知中心](create-notification-hub-portal.md)。 

## <a name="configure-microsoft-push-notification-service-mpns"></a>配置 Microsoft 推送通知服务 (MPNS)

以下过程提供配置通知中心的 Microsoft 推送通知服务 (MPNS) 设置的步骤： 

1. 在 Azure 门户中，在**通知中心**页上，选择**Windows Phone (MPNS)** 在左侧菜单中。
1. 启用未经身份验证或经过身份验证的通知：

   a. 若要启用未经身份验证的推送通知，请选择“启用未经身份验证的推送” > “保存”。

      ![显示如何启用未经身份验证的推送通知的屏幕截图](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. 启用经过身份验证的推送通知：
      * 在工具栏上选择“上传证书”。
      * 选择“文件”图标，然后选择证书文件。
      * 输入证书的密码。
      * 选择“确定”。
      * 在“Windows Phone (MPNS)”页上选择“保存”。

## <a name="next-steps"></a>后续步骤
有关使用 Azure 通知中心和 Microsoft 推送通知服务 (MPNS) 向 Windows Phone 设备推送通知的分步说明的教程，请参阅[使用通知将推送通知到 Windows Phone 应用程序中心](notification-hubs-windows-mobile-push-notifications-mpns.md)。

