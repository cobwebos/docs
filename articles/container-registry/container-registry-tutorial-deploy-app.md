---
title: "Azure 容器注册表教程 - 从 Azure 容器注册表部署 Web 应用"
description: "使用异地复制的 Azure 容器注册表中的容器映像部署基于 Linux 的 Web 应用。 由三个部分构成的教程系列的第二部分。"
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: neilpeterson
tags: acr, azure-container-registry, geo-replication
keywords: "Docker, 容器, 注册表, Azure"
ms.service: container-registry
ms.devlang: 
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 90d4b51dfaad409298f72887480dfaf827aef9f0
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2017
---
# <a name="deploy-web-app-from-azure-container-registry"></a>从 Azure 容器注册表部署 Web 应用

本文是由三个部分构成的教程系列的第二部分。 在[第一部分](container-registry-tutorial-prepare-registry.md)中，已创建一个专用的异地复制容器注册表，已从源生成容器映像并将其推送到该注册表。 本文将容器部署到位于两个不同 Azure 区域的两个 Web 应用实例中，以利用异地复制注册表的“紧靠网络”特点。

本教程（教程系列的第二部分）的内容包括：

> [!div class="checklist"]
> * 将容器映像部署到两个用于容器的 Web 应用实例
> * 验证已部署的应用程序

如果尚未创建异地复制的注册表并将容器化示例应用程序的映像推送到注册表，请返回本教程系列的前一篇教程：[准备异地复制的 Azure 容器注册表](container-registry-tutorial-prepare-registry.md)。

在教程系列的下一部分，我们将会更新该应用程序，然后将新的容器映像推送到注册表。 最后，浏览到每个正在运行的 Web 应用实例，以查看所做的更改是否自动反映在这两个实例中，并查看 Azure 容器注册表异地复制和 Webhook 的运行情况。

## <a name="automatic-deployment-to-web-apps-for-containers"></a>自动部署到用于容器的 Web 应用

Azure 容器注册表支持直接将容器化应用程序部署到[用于容器的 Web 应用](../app-service/containers/index.yml)。 在本教程中，我们使用 Azure 门户将前一篇教程中创建的容器映像部署到位于不同 Azure 区域中的两个 Web 应用计划。

如果通过注册表中的容器映像部署 Web 应用，并且同一区域中有一个异地复制的注册表，则 Azure 容器注册表会创建一个映像部署 [Webhook](container-registry-webhook.md)。 将新映像推送到容器存储库时，该 Webhook 会拾取更改，并自动将新容器映像部署到 Web 应用。

## <a name="deploy-a-web-app-for-containers-instance"></a>部署用于容器的 Web 应用实例

此步骤在“美国西部”区域创建一个用于容器的 Web 应用实例。

登录到 [Azure 门户](https://portal.azure.com)，并导航到在前一篇教程中创建的注册表。

选择“存储库” > “acr-helloworld”，右键单击“标记”下的“v1”标记，并选择“部署到 Web 应用”。

![在 Azure 门户中部署到应用服务][deploy-app-portal-01]

在显示的“容器的 Web 应用”下，为每项设置指定以下值：

| 设置 | 值 |
|---|---|
| **站点名称** | Web 应用的全局唯一名称。 本示例使用格式 `<acrName>-westus` 来方便标识注册表，以及要从中部署 Web 应用的区域。 |
| **资源组** | **使用现有项** > `myResourceGroup` |
| **应用服务计划/位置** | 在“美国西部”区域创建名为 `plan-westus` 的新计划。 |
| **图像** | `acr-helloworld:v1`

选择“创建”，将该 Web 应用预配到“美国西部”区域。

![Azure 门户中的“Linux 上的 Web 应用”配置][deploy-app-portal-02]

## <a name="view-the-deployed-web-app"></a>查看已部署的 Web 应用

部署完成后，可在浏览器中导航到应用程序的 URL 来查看正在运行的应用程序。

在门户中选择“应用服务”，再选择在上一步骤中预配的 Web 应用。 在本示例中，该 Web 应用名为 *uniqueregistryname-westus*。

在“应用服务”概述的右上方选择该 Web 应用的超链接 URL，在浏览器中查看正在运行的应用程序。

![Azure 门户中的“Linux 上的 Web 应用”配置][deploy-app-portal-04]

从异地复制的容器注册表部署 Docker 映像后，站点会显示一个图像，表示托管容器注册表的 Azure 区域。

![在浏览器中查看已部署的 Web 应用程序][deployed-app-westus]

## <a name="deploy-second-web-app-for-containers-instance"></a>部署第二个用于容器的 Web 应用实例

使用上一部分中所述的过程，将第二个 Web 应用部署到“美国东部”区域。 在“容器的 Web 应用”下，指定以下值：

| 设置 | 值 |
|---|---|
| **站点名称** | Web 应用的全局唯一名称。 本示例使用格式 `<acrName>-eastus` 来方便标识注册表，以及要从中部署 Web 应用的区域。 |
| **资源组** | **使用现有项** > `myResourceGroup` |
| **应用服务计划/位置** | 在“美国东部”区域创建名为 `plan-eastus` 的新计划。 |
| **图像** | `acr-helloworld:v1`

选择“创建”，将 Web 应用预配到“美国东部”区域。

![Azure 门户中的“Linux 上的 Web 应用”配置][deploy-app-portal-06]

## <a name="view-the-deployed-web-app"></a>查看已部署的 Web 应用

如前所述，可在浏览器中导航到应用程序的 URL 来查看正在运行的应用程序。

在门户中选择“应用服务”，再选择在上一步骤中预配的 Web 应用。 在本示例中，该 Web 应用名为 *uniqueregistryname-eastus*。

在“应用服务概述”的右上角选择该 Web 应用的超链接 URL，在浏览器中查看正在运行的应用程序。

![Azure 门户中的“Linux 上的 Web 应用”配置][deploy-app-portal-07]

从异地复制的容器注册表部署 Docker 映像后，站点会显示一个图像，表示托管容器注册表的 Azure 区域。

![在浏览器中查看已部署的 Web 应用程序][deployed-app-eastus]

## <a name="next-steps"></a>后续步骤

本教程已从异地复制的 Azure 容器注册表部署了两个用于容器的 Web 应用实例。 我们已遵循本教程中的步骤执行以下操作：

> [!div class="checklist"]
> * 已将容器映像部署到两个用于容器的 Web 应用实例
> * 验证了已部署的应用程序

请继续学习下一篇教程更新容器映像，将新的容器映像部署到容器注册表，然后验证两个区域中运行的 Web 应用是否已自动更新。

> [!div class="nextstepaction"]
> [将更新部署到异地复制的容器映像](./container-registry-tutorial-deploy-update.md)

<!-- IMAGES -->
[deploy-app-portal-01]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-01.png
[deploy-app-portal-02]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-02.png
[deploy-app-portal-03]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-03.png
[deploy-app-portal-04]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-04.png
[deploy-app-portal-05]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-05.png
[deploy-app-portal-06]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-06.png
[deploy-app-portal-07]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-07.png
[deployed-app-westus]: ./media/container-registry-tutorial-deploy-app/deployed-app-westus.png
[deployed-app-eastus]: ./media/container-registry-tutorial-deploy-app/deployed-app-eastus.png