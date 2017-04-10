---
title: "将 Web 应用连接到内容交付网络 | Microsoft Docs"
description: "将 Web 应用连接到内容交付网络，以便从边缘节点传送静态文件。"
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: d616ca49da7a909a1a104409c5845f39c4a5377a
ms.lasthandoff: 04/04/2017

---
# <a name="connect-a-web-app-to-a-content-delivery-network"></a>将 Web 应用连接到内容交付网络

本教程将创建一个 Azure CDN 配置文件和一个 Azure CDN 终结点，用于通过 Azure CDN pop 位置提供 Web 应用中的静态文件。

> [!TIP]
> 查看 [Azure CDN pop 位置](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations)的最新列表。
>

## <a name="step-1---login-to-azure-portal"></a>步骤 1 - 登录到 Azure 门户

首先，打开偏好的浏览器并浏览到 Azure [门户](https://portal.azure.com)。

## <a name="step-2---create-a-cdn-profile"></a>步骤 2 - 创建 CDN 配置文件

在左侧导航栏中单击 `+ New` 按钮，然后单击“Web + 移动”。 在“Web + 移动”类别下面选择“CDN”。

指定“名称”、“位置”、“资源组”、“定价层”，然后单击“创建”。

在左侧导航栏中打开资源组中心，然后选择“myResourceGroup”。 在资源列表中，选择“myCDNProfile”。

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>步骤 3 - 创建 CDN 终结点

在搜索框旁边的命令中单击 `+ Endpoint`，启动“创建终结点”边栏选项卡。

指定“名称”、“原点类型”、“原点主机名”，然后单击“添加”。

随后将创建终结点。创建内容交付网络终结点后，状态将更新为“正在运行”。

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---leveraging-cdn"></a>步骤 4 - 利用 CDN

运行终结点后，让我们通过浏览到 Web 服务器上的静态文件，然后将主机名从 `http://<app_name>.azurewebsites.net/path/to-static-file` 更改为 `http://<endpoint_name>.azureedge.net/path/to-static-file`，来验证 pop 服务器上是否出现了该内容。

![app-service-web-url-to-cdn-endpoint-url](media/app-service-web-tutorial-content-delivery-network/app-service-web-url-to-cdn-endpoint-url.png)

## <a name="next-steps"></a>后续步骤


