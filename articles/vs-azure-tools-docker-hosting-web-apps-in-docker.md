---
title: "将 ASP.NET Core Linux Docker 容器部署到远程 Docker 主机 | Microsoft Docs"
description: "了解如何使用 Visual Studio Tools for Docker 将 ASP.NET Core Web 应用部署到在 Azure Docker 主机 Linux VM 上运行的 Docker 容器"
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: 
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2016
ms.author: mlearned
ms.translationtype: Human Translation
ms.sourcegitcommit: be747170a0d8a7a6defd790a3f8a122c4d397671
ms.openlocfilehash: 4a87ee69f23779bf4f6f5db40bc05edbcfc7668d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/23/2017

---
# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>将 ASP.NET 容器部署到远程 Docker 主机
## <a name="overview"></a>概述
Docker 是轻型容器引擎，在某些方面类似于虚拟机，你可以将其用于托管应用程序和服务。
本教程将指导你完成使用 [Visual Studio Tools for Docker](https://docs.microsoft.com/en-us/dotnet/articles/core/docker/visual-studio-tools-for-docker) 扩展在 Azure 上通过 PowerShell 将 ASP.NET Core 应用部署到 Docker 主机。

## <a name="prerequisites"></a>先决条件
以下是完成本教程所要做好的准备：

* 创建 Azure Docker 主机 VM，如 [How to use docker-machine with Azure](virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（如何将 docker-machine 与 Azure 配合使用）中所述
* 安装最新版本的 [Visual Studio](https://www.visualstudio.com/downloads/)
* 下载 [Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)
* 安装[适用于 Windows 的 Docker](https://docs.docker.com/docker-for-windows/install/)

## <a name="1-create-an-aspnet-core-web-app"></a>1.创建一个 ASP.NET Core Web 应用
以下步骤将指导你完成创建将在本教程中使用的基本 ASP.NET Core 应用。

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2.添加 Docker 支持
[!INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3.使用 DockerTask.ps1 PowerShell 脚本
1. 打开 PowerShell 提示符，并转到项目的根目录。 
   
   ```
   PS C:\Src\WebApplication1>
   ```
2. 验证远程主机是否正在运行。 你应看到状态 =“正在运行” 
   
   ```
   docker-machine ls
   NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
   MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
   ```
   
3. 使用 -Build 参数生成应用
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
   ```  

   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
   > ```  
   > 
   > 
4. 使用 -Run 参数运行该应用
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
   ```
   
   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
   > ```
   > 
   > 
   
   Docker 完成后，你应看到与下面类似的结果：
   
   ![查看你的应用][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png

