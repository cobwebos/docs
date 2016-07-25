<properties
   pageTitle="将 ASP.NET Core Linux Docker 容器部署到远程 Docker 主机 | Azure"
   description="了解如何使用 Visual Studio Tools for Docker 将 ASP.NET Core Web 应用部署到在 Azure Docker 主机 Linux VM 上运行的 Docker 容器"   
   services="azure-container-service"
   documentationCenter=".net"
   authors="allclark"
   manager="douge"
   editor=""/>

<tags
   ms.service="azure-container-service"
   ms.date="06/08/2016"
   wacn.date=""/>

# 将 ASP.NET 容器部署到远程 Docker 主机

## 概述
Docker 是轻型容器引擎，在某些方面类似于虚拟机，你可以将其用于托管应用程序和服务。
本教程将指导你完成使用 [Visual Studio 2015 Tools for Docker](http://aka.ms/DockerToolsForVS) 扩展在 Azure 上通过 PowerShell 将 ASP.NET Core 应用部署到 Docker 主机。

## 先决条件
以下是完成本教程所要做好的准备：

- 创建 Azure Docker 主机 VM，如 [How to use docker-machine with Azure](/documentation/articles/virtual-machines-linux-docker-machine/)（如何将 docker-machine 与 Azure 配合使用）中所述
- 安装 [Visual Studio 2015 Update 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- 安装 [Visual Studio 2015 Tools for Docker - 预览版](http://aka.ms/DockerToolsForVS)

## 1\.创建一个 ASP.NET 5 Web 应用
以下步骤将指导你完成创建将在本教程中使用的基本 ASP.NET 5 应用。

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 2\.添加 Docker 支持

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## 3\.使用 DockerTask.ps1 PowerShell 脚本 

1.  打开 PowerShell 提示符，并转到项目的根目录。

    ```
    PS C:\Src\WebApplication1>
    ```

1.  验证远程主机是否正在运行。你应看到状态 =“正在运行”

    ```
    docker-machine ls
    NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
    MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
    ```

    > [AZURE.NOTE] 如果你使用的是 Docker Beta 版，则不会在此处列出你的主机。

1.  使用 -Build 参数生成应用

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
    ```  

    > [AZURE.NOTE] 如果你使用的是 Docker Beta 版，请忽略 -Machine 参数
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
    > ```  


1.  使用 -Run 参数运行该应用

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
    ```

    > [AZURE.NOTE] 如果你使用的是 Docker Beta 版，请忽略 -Machine 参数
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
    > ```

	Docker 完成后，你应看到与下面类似的结果：

    ![查看你的应用][3]

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png

<!---HONumber=AcomDC_0718_2016-->