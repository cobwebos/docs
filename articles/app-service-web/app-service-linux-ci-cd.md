---
title: "Linux 上 Azure Web 应用的持续部署 | Microsoft Docs"
description: "如何在 Linux 上的 Azure Web 应用中设置连续部署。"
keywords: azure app service, linux, oss, acr
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
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: 6b6c173c6c4bb3f670c54208c80e6d966a1f396e
ms.contentlocale: zh-cn
ms.lasthandoff: 07/19/2017

---
# <a name="continuous-deployment-with-azure-web-app-on-linux"></a>在 Linux 上的 Azure Web 应用中进行持续部署

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

在本教程中，通过托管 [Azure 容器注册表](https://azure.microsoft.com/en-us/services/container-registry/)存储库或 [Docker 中心](https://hub.docker.com)为自定义容器映像配置持续部署。

## <a name="step-1---sign-in-to-azure"></a>步骤 1 - 登录 Azure

通过 http://portal.azure.com 登录到 Azure 门户

## <a name="step-2---enable-docker-hub-continuous-deployment"></a>步骤 2 - 启用 Docker 中心持续部署

在 [Azure 门户](https://portal.azure.com/)中，单击页面左侧的“应用服务”选项。

单击要为其配置 Docker 中心持续部署的应用的名称。

在“应用设置”中，添加名为 `DOCKER_ENABLE_CI`、值为 `true` 的应用设置。

![插入应用设置的图像](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---prepare-webhook-url"></a>步骤 3 - 准备 Webhook URL

对于 Webhook URL，需要使用以下终结点：`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`。

可以通过使用 Azure 门户下载 Web 应用发布配置文件来获取 `publishingusername` 和 `publishingpwd`。

![插入“添加 webhook 2”的图像](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="step-4---add-a-web-hook"></a>步骤 4 - 添加 Webhook

### <a name="azure-container-registry"></a>Azure 容器注册表

在注册表门户边栏选项卡中单击“Webhook”，然后单击“添加”创建新的 Webhook。 在“创建 Webhook”边栏选项卡中，为 Webhook 指定名称。 对于 Webhook URI，需要提供从步骤 3 中获得的 URL

请确保将范围定义为包含容器映像的存储库。

![插入 Webhook 的映像](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

更新映像时，将自动使用新映像更新 Web 应用。

### <a name="docker-hub"></a>Docker 中心

在“Docker 中心”页中，单击“Webhook”，然后单击“创建 WEBHOOK”。

![插入“添加 webhook 1”的图像](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

对于 Webhook URL，需要提供从步骤 3 获得的 URL

![插入“添加 webhook 2”的图像](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

更新映像时，将自动使用新映像更新 Web 应用。

## <a name="next-steps"></a>后续步骤
* [什么是 Linux 上的 Azure Web 应用？](./app-service-linux-intro.md)
* [在 Linux 上的 Azure Web 应用中创建应用](./app-service-linux-how-to-create-web-app.md)
* [Azure 容器注册表](https://azure.microsoft.com/en-us/services/container-registry/)
* [在 Linux 上的 Azure Web 应用中使用针对 Node.js 的 PM2 配置](app-service-linux-using-nodejs-pm2.md)
* [在 Linux 上的 Azure Web 应用中使用 .NET Core](app-service-linux-using-dotnetcore.md)
* [在 Linux 上的 Azure Web 应用中使用 Ruby](app-service-linux-ruby-get-started.md)
* [如何使用 Linux 上 Azure Web 应用的自定义 Docker 映像](./app-service-linux-using-custom-docker-image.md)
* [Linux 上的 Azure 应用服务 Web 应用常见问题解答](./app-service-linux-faq.md) 




