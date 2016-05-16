<properties
   pageTitle="将 ASP.NET 容器部署到远程 Docker 主机 | Azure"
   description="了解如何使用 Visual Studio Tools for Docker 将 ASP.NET 5 Web 应用发布到在 Azure Docker 主机计算机上运行的 Docker 容器"   
   services="visual-studio-online"
   documentationCenter=".net"
   authors="tomarcher"
   manager="douge"
   editor=""/>

<tags
   ms.service="visual-studio-online"
   ms.date="03/26/2016"
   wacn.date=""/>

# 将 ASP.NET 容器部署到远程 Docker 主机

## 概述
Docker 是轻型容器引擎，在某些方面类似于虚拟机，你可以将其用于托管应用程序和服务。Visual Studio 支持在 Ubuntu、CoreOS 和 Windows 上的 Docker。本教程将指导你完成使用 [Visual Studio 2015 Tools for Docker](http://aka.ms/DockerToolsForVS) 扩展在 Azure 上将 ASP.NET 5 应用发布到 Docker 主机。

## 1\.先决条件
以下是完成本教程所要做好的准备：

- 创建 Azure Docker 主机 VM，如[如何将 docker-machine 与 Azure 一起使用](/documentation/articles/virtual-machines-linux-classic-docker-machine)中所述
- 安装 [Visual Studio 2015](https://www.visualstudio.com/zh-cn/downloads/download-visual-studio-vs.aspx)
- 安装 [Visual Studio 2015 Tools for Docker - 预览版](http://aka.ms/DockerToolsForVS)

## 2\.创建一个 ASP.NET 5 Web 应用
以下步骤将指导你完成创建将在本教程中使用的基本 ASP.NET 5 应用。

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 3\.添加 Docker 支持

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## 4\.指向远程 Docker 主机

1.  在 Visual Studio“解决方案资源管理器”中，找到“属性”文件夹并将其展开。
1.  打开 “Docker.props” 文件。

    ![打开 Docker.props 文件][0]

1.  将 **DockerMachineName** 的值更改为你的远程 Docker 主机的名称。如果你不知道你的远程 Docker 主机的名称，请在 Windows PowerShell 的提示符处运行 ```docker-machine ls```。为所需的主机使用列在“名称”列下的值。

    ![更改 Docker 计算机名称][1]

1.  重新启动 Visual Studio。

## 5\.配置 Azure Docker 主机终结点
在将你的应用从 Visual Studio 部署到 Azure 之前，将终结点 80 添加到 Docker 主机虚拟机，以便你可以稍后从浏览器查看应用。此操作可以通过经典 Azure 门户或 Windows PowerShell 完成：

- **使用经典 Azure 门户配置 Azure Docker 主机终结点**

    1.  浏览到[经典 Azure 门户](https://manage.windowsazure.cn/)。 
    
    1.  点击“虚拟机”。
    
    1.  选择你的 Docker 主机虚拟机。
    
    1.  点击“终结点”选项卡。
    
    1.  点击“添加”（位于页面底部）。
    
    1.  按照说明来公开端口 80，它在默认情况下由部署脚本使用。

- **使用 Windows PowerShell 配置 Azure Docker 主机终结点**

    1. 打开 Windows PowerShell
    1. 在 Windows PowerShell 提示符处输入以下命令（更改尖括号中的值以匹配你的环境）：  

        ```PowerShell
        C:\PS>Get-AzureVM -ServiceName "<your_cloud_service_name>" -Name "<your_vm_name>" | Add-AzureEndpoint -Name "<endpoint_name>" -Protocol "tcp" -PublicPort 80 -LocalPort 80 | Update-AzureVM
        ```

## 6\.构建并运行应用程序
当部署到远程主机时，用于“编辑和刷新”开发的卷映射功能将不能正常运行。因此，你需要在构建应用程序时使用发布配置来避免卷映射配置。请按照以下步骤运行你的应用程序。

1.  从 Visual Studio 工具栏中，选择“发布”配置

1.  将启动目标更改为“Docker”。

1.  点击“Docker”图标，构建并运行应用程序。

![启动应用][2]

你应该会看到与下面类似的结果。

![查看你的应用][3]

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png
<!---HONumber=Mooncake_0509_2016-->