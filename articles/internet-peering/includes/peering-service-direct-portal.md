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
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81687046"
---
1. 选择要为 Azure 对等互连服务启用的对等连接。 然后选择 **...**  > **编辑连接**。
    > [!div class="mx-imgBorder"]
    > ![对等连接编辑连接](../media/setup-direct-modify-editconnection.png)
1. 在 "用于 **对等互连服务**" 下选择 " **已启用** "，然后选择 " **保存**"。
    > [!div class="mx-imgBorder"]
    > ![对等互连启用对等互连服务](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. 在 " **概述** " 屏幕上，可以看到部署详细信息。 部署完成后，选择 " **前往资源**"。
    > [!div class="mx-imgBorder"]
    > ![你的部署已完成](../media/setup-direct-modify-overview-deployment-complete.png)

1. 在 " **已注册的前缀** " 窗格上，选择 " **添加已注册的前缀**"。
    > [!div class="mx-imgBorder"]
    > ![添加已注册的前缀](../media/setup-direct-modify-add-registered-prefix.png)
1. 通过选择 **名称** 和 **前缀** 来注册前缀，并选择 " **保存**"。
    > [!div class="mx-imgBorder"]
    >  ![注册前缀](../media/setup-direct-modify-register-a-prefix.png) 

1. 创建前缀后，会在 **已注册前缀**列表中看到它。 选择前缀的 **名称** 以查看更多详细信息。
    > [!div class="mx-imgBorder"]
    > ![注册的前缀和连接](../media/setup-direct-modify-registered-prefixes.png)
1. 在 "已注册的前缀" 页上，可以看到完整的详细信息，其中包括每个前缀的 **前缀键** 。 此密钥必须提供给客户从其提供商 ISP 分配此前缀。 然后，客户可以使用此密钥在其订阅中注册其前缀。
    > [!div class="mx-imgBorder"]
    > ![前缀和前缀键](../media/setup-direct-modify-registered-prefix-detail.png)