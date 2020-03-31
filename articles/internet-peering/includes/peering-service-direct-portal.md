---
title: include 文件
titleSuffix: Azure
description: include 文件
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 60752cf1b3c05ab7817083e70310ba7b40227dec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129974"
---
1. 单击要为对等互连服务启用的对等互连连接，然后单击 **...** > **编辑连接**按钮。
    > [!div class="mx-imgBorder"]
    > ![对等连接编辑](../media/setup-direct-modify-editconnection.png)
1. 在"***对等服务使用"*** 一节中，单击"**已启用"** 并**保存**。
    > [!div class="mx-imgBorder"]
    > ![对等互连连接启用对等互连服务](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. 在"概述"屏幕中，您将看到部署详细信息。 部署完成后，单击"**转到资源**"。
    > [!div class="mx-imgBorder"]
    > ![您的部署已完成](../media/setup-direct-modify-overview-deployment-complete.png)

1. 然后，您将在"设置**已注册前缀"** 下看到 。 单击 **"添加已注册前缀**"。
    > [!div class="mx-imgBorder"]
    > ![已注册的前缀和连接](../media/setup-direct-modify-add-registered-prefix.png)
1. 通过选择**名称**和前缀注册前**缀**，然后单击"**保存"**
    > [!div class="mx-imgBorder"]
    >  ![注册前缀](../media/setup-direct-modify-register-a-prefix.png) 

1. 创建前缀后，您将在"已注册前缀"列表中看到它。 单击前缀**的名称**以查看更多详细信息。
    > [!div class="mx-imgBorder"]
    > ![已注册的前缀和连接](../media/setup-direct-modify-registered-prefixes.png)
1. 在已注册的前缀页中，您将看到包含每个**前缀的前缀键**的完整详细信息。 此密钥需要提供给从其提供商 ISP 分配此前缀的客户。 然后，客户可以使用此密钥在其订阅中注册其前缀。
    > [!div class="mx-imgBorder"]
    > ![带前缀键的前缀](../media/setup-direct-modify-registered-prefix-detail.png)