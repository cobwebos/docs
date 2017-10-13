---
title: "使用 Azure CLI 2.0 管理用于容器的 Web 应用 | Microsoft Docs"
description: "使用 Azure CLI 管理用于容器的 Web 应用。"
keywords: "azure app service、Web 应用、cli、linux、oss"
services: app-service
documentationCenter: 
author: ahmedelnably
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: aelnably
ms.openlocfilehash: 5213bec66095d50ef29cc3bec0a7b6307614dc92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>使用 Azure CLI 管理用于容器的 Web 应用

通过本文中的命令，能够使用 Azure CLI 2.0 创建和管理用于容器的 Web 应用。
你可以通过两种方式开始使用 CLI 的新版本：

* 在计算机上[安装 Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)。
* 使用 [Azure Cloud Shell（预览版）](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>创建 Linux 应用服务计划

若要创建 Linux 应用服务计划，你可以使用以下命令：

```azurecli-interactive
az appservice plan create -n appname -g rgname --islinux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>创建自定义 Docker 容器 Web 应用

若要创建 Web 应用并将其配置为运行自定义 Docker 容器，请使用以下命令：

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>激活 Docker 容器日志记录

若要激活 Docker 容器日志记录，可以使用以下命令：

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>更改用于容器应用的现有 Web 应用的自定义 Docker 容器

若要更改以前创建的应用，从当前的 Docker 映像更改为新映像，请可以使用以下命令：

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>使用专用注册表中的 Docker 映像

可以配置应用程序以使用私有注册表中的映像。 需要为注册表、用户名和密码提供 url。 可以使用以下命令实现此操作：

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>启用自定义 Docker 映像的连续部署

使用以下命令，可以启用 CD 功能，并获取 webhook url。 此 url 可用于配置 DockerHub 或 Azure 容器注册表存储库。

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>使用我们的一个内置运行时框架创建用于容器应用的 Web 应用

若要创建用于容器应用的 PHP 5.6 Web 应用，可使用以下命令。

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>更改用于容器应用的现有 Web 应用的框架版本

若要更改以前创建的应用，从当前的框架版本更改为 Node.js 6.11，请使用以下命令：

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>设置 Web 应用的 Git 部署

若要为应用设置 Git 部署，请使用以下命令：

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>后续步骤

* [什么是用于容器的 Azure Web 应用？](app-service-linux-intro.md)
* [安装 Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)
* [Azure Cloud Shell（预览版）](../../cloud-shell/overview.md)
* [设置 Azure 应用服务中的过渡环境](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [使用用于容器的 Azure Web 应用进行持续部署](app-service-linux-ci-cd.md)
