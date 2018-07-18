---
title: 向 Azure 容器注册表 (ACR) 部署 ASP.NET Docker 容器 | Microsoft Docs
description: 了解如何使用 Visual Studio Tools for Docker 将 ASP.NET Core Web 应用部署到容器注册表
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: ''
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/21/2018
ms.author: mlearned
ms.openlocfilehash: 58df17b17de1d93683875b68dd7c6c087bc6d16d
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972302"
---
# <a name="deploy-an-aspnet-container-to-a-container-registry-using-visual-studio"></a>使用 Visual Studio 将 ASP.NET 容器部署到容器注册表
## <a name="overview"></a>概述
Docker 是轻型容器引擎，在某些方面类似于虚拟机，可以将其用于托管应用程序和服务。
本教程介绍如何使用 Visual Studio 将容器化应用程序发布到 [ Azure 容器注册表](https://azure.microsoft.com/services/container-registry)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/dotnet/?utm_source=acr-publish-doc&utm_medium=docs&utm_campaign=docs)。

## <a name="prerequisites"></a>先决条件
完成本教程：

* 安装带有“ASP.NET 和 Web 开发”工作负载的最新版本 [Visual Studio 2017](https://azure.microsoft.com/downloads/)
* 安装[适用于 Windows 的 Docker](https://docs.docker.com/docker-for-windows/install/)

## <a name="1-create-an-aspnet-core-web-app"></a>1.创建一个 ASP.NET Core Web 应用
以下步骤将指导你完成创建基本 ASP.NET Core 应用（将在本教程中使用）的过程。

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-publish-your-container-to-azure-container-registry"></a>2.将容器发布到 Azure 容器注册表
1. 在解决方案资源管理器中右键单击项目，并选择“发布”。
2. 在发布目标对话框上，选择“容器注册表”选项卡。
3. 选择“新建 Azure 容器注册表”并单击“发布”。
4. 在“创建新 Azure 容器注册表”中填写所需的值。

    | 设置      | 建议的值  | 说明                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS 前缀** | 全局唯一名称 | 用于唯一标识容器注册表的名称。 |
    | **订阅** | 选择订阅 | 要使用的 Azure 订阅。 |
    | [资源组](../articles/azure-resource-manager/resource-group-overview.md) | myResourceGroup |  要在其中创建容器注册表的资源组的名称。 选择“新建”创建新的资源组。|
    | **[SKU](https://docs.microsoft.com/azure/container-registry/container-registry-skus)** | 标准 | 容器注册表的服务层  |
    | **注册表位置** | 靠近你的位置 | 在你附近或将使用容器注册表的其他服务附近的[区域](https://azure.microsoft.com/regions/)中，选择位置。 |
    ![Visual Studio 的创建 Azure 容器注册表对话框][0]
5. 单击“创建” 

现在可以将容器从注册表中拖放到任何能够运行 Docker 映像的主机上，例如[Azure 容器实例](./container-instances/container-instances-tutorial-deploy-app.md)。

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/vs-acr-provisioning-dialog.png
