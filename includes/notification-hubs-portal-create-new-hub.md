---
title: include 文件
description: include 文件
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 22c1d24042072de5d57d41da379a5fad18180de7
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972500"
---
1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“创建资源” > “移动” > “通知中心”。
   
      ![Azure 门户 - 创建通知中心](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. 在“通知中心”框中，键入一个唯一名称。 选择“区域”、“订阅”和“资源组”（如果已经有一个）。 
   
      如果尚无服务总线命名空间，可以使用基于中心名称创建的默认名称（如果该命名空间名称可用）。
    
      如果已有要在其中创建中心的服务总线命名空间，请执行以步骤

    a. 在“命名空间”区域中，选择“选择现有”链接。 
   
    b. 选择**创建**。
   
      ![Azure 门户 - 设置通知中心属性](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

4. 选择“通知”（钟形图标），然后选择“转到资源”。 

      ![Azure 门户 - 通知 -> 转到资源](./media/notification-hubs-portal-create-new-hub/notification-go-to-resource.png)    
5. 从列表中选择“访问策略”。 记下向你提供的两个连接字符串。 稍后在处理推送通知时需要它们。

      >[!IMPORTANT]
      >请**勿**在应用程序中使用 DefaultFullSharedAccessSignature。 这只能在后端使用。
      >
   
      ![Azure 门户 - 通知中心连接字符串](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

