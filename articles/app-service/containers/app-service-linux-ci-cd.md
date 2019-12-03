---
title: CI/CD 到自定义 Linux 容器
description: 了解如何在 Azure App Service 中设置自定义 Linux 容器的持续部署。 Docker 中心和 ACR 支持连续部署。
keywords: azure 应用服务, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687635"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>使用用于容器的 Web 应用进行持续部署

在本教程中，通过托管 [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)存储库或 [Docker 中心](https://hub.docker.com)为自定义容器映像配置持续部署。

## <a name="enable-continuous-deployment-with-acr"></a>使用 ACR 启用持续部署

![ACR Webhook 的屏幕截图](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择页面左侧的“应用服务”选项。
3. 选择要为其配置持续部署的应用的名称。
4. 在“容器设置”页上，选择“单个容器”
5. 选择“Azure 容器注册表”
6. 选择“持续部署”>“启用”
7. 选择“保存”以启用持续部署。

## <a name="use-the-acr-webhook"></a>使用 ACR Webhook

启用持续部署后，可以在 Azure 容器注册表 Webhook 页上查看新创建的 Webhook。

![ACR Webhook 的屏幕截图](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

在容器注册表中，单击“Webhook”以查看当前的 Webhook。

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>使用 Docker 中心启用持续部署（可选）

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择页面左侧的“应用服务”选项。
3. 选择要为其配置持续部署的应用的名称。
4. 在“容器设置”页上，选择“单个容器”
5. 选择“Docker 中心”
6. 选择“持续部署”>“启用”
7. 选择“保存”以启用持续部署。

![应用设置的屏幕截图](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

复制 Webhook URL。 若要添加用于 Docker 中心的 Webhook，请按照<a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">用于 Docker 中心的 Webhook</a> 进行操作。

## <a name="next-steps"></a>后续步骤

* [Linux 上的 Azure 应用服务简介](./app-service-linux-intro.md)
* [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)
* [在 Linux 应用服务中创建 .NET Core Web 应用](quickstart-dotnetcore.md)
* [使用 Linux 应用服务创建 Ruby Web 应用](quickstart-ruby.md)
* [在用于容器的 Web 应用中部署 Docker/Go Web 应用](quickstart-docker-go.md)
* [Linux 上的 Azure 应用服务常见问题解答](./app-service-linux-faq.md)
* [使用 Azure CLI 管理用于容器的 Web 应用](./app-service-linux-cli.md)
