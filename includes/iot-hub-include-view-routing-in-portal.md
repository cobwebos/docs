---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 178236f774b0e1a6fe20c73afe0e7fd6965e67a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67235248"
---
设置终结点和消息路由以后，即可在门户中查看其配置。 登录 [Azure 门户](https://portal.azure.com)，转到“资源组”。  接下来选择你的资源组，然后选择中心（在本教程中，中心名称以 `ContosoTestHub` 开头）。 此时会看到“IoT 中心”窗格。

![IoT 中心属性平面](./media/iot-hub-include-view-routing-in-portal/01-show-hub-properties.png)

在适用于 IoT 中心的选项中，选择“消息路由”。  此时会显示已成功设置的路由。

![已设置的路由](./media/iot-hub-include-view-routing-in-portal/02-show-message-routes.png)

在“消息路由”屏幕上选择“自定义终结点”，查看为路由定义的终结点。

![为路由设置的终结点](./media/iot-hub-include-view-routing-in-portal/03-show-routing-endpoints.png)