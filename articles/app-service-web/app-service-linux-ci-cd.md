---
title: "使用 Linux 上的 Azure Web 应用进行 Docker 中心持续部署 | Microsoft Docs"
description: "如何在 Linux 上的 Azure Web 应用中设置连续部署。"
keywords: "azure 应用服务, linux, oss"
services: app-service
documentationcenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 68e11f07520bbe8da6812a34fe95708fc9e932ac
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="docker-hub-continuous-deployment-with-azure-web-app-on-linux"></a>使用 Linux 上的 Azure Web 应用进行 Docker 中心持续部署

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

在本教程中，将为 [Docker 中心](https://hub.docker.com)内的自定义容器映像配置持续部署。

## <a name="step-1---log-in-to-azure"></a>步骤 1 - 登录 Azure

通过 http://portal.azure.com 登录到 Azure 门户

## <a name="step-2---enable-docker-hub-continuous-deployment"></a>步骤 2 - 启用 Docker 中心持续部署

在 [Azure 门户](https://portal.azure.com/)中，单击页面左侧的“应用服务”选项。

单击要为其配置 Docker 中心持续部署的应用的名称。

在“应用设置”中，添加名为 `DOCKER_ENABLE_CI`、值为 `true` 的应用设置。

![插入应用设置的图像](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---add-a-web-hook-to-docker-hub"></a>步骤 3 - 将 Web 挂钩添加到 Docker 中心

在“Docker 中心”页中，单击“Webhook”，然后单击“创建 WEBHOOK”。

![插入“添加 webhook 1”的图像](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

对于 Webhook URL，需要使用以下终结点：`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`。

![插入“添加 webhook 2”的图像](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

可以通过使用 Azure 门户下载 Web 应用发布配置文件来获取 `publishingusername` 和 `publishingpwd`。

![插入“添加 webhook 2”的图像](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

更新映像时，将自动使用新映像更新 Web 应用。

## <a name="next-steps"></a>后续步骤
* [什么是 Linux 上的 Azure Web 应用？](./app-service-linux-intro.md)
* [在 Linux 上的 Azure Web 应用中创建应用](./app-service-linux-how-to-create-web-app.md)
* [在 Linux 上的 Azure Web 应用中使用针对 Node.js 的 PM2 配置](app-service-linux-using-nodejs-pm2.md)
* [在 Linux 上的 Azure Web 应用中使用 .NET Core](app-service-linux-using-dotnetcore.md)
* [在 Linux 上的 Azure Web 应用中使用 Ruby](app-service-linux-ruby-get-started.md)
* [如何使用 Linux 上 Azure Web 应用的自定义 Docker 映像](./app-service-linux-using-custom-docker-image.md)
* [Linux 上的 Azure App Service Web 应用常见问题解答](./app-service-linux-faq.md) 




