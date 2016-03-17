<properties
   pageTitle="设置开发环境 | Microsoft Azure"
   description="安装运行时、SDK 和工具并创建本地开发群集。完成此设置后，你就可以开始生成应用程序。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="02/09/2016"
   wacn.date=""/>

# 准备开发环境
 若要在开发计算机上生成并运行 [Azure Service Fabric 应用程序][1]，你需要安装运行时、SDK 和工具。此外，还需执行 SDK 中包含的 Windows PowerShell 脚本。

## 先决条件
### 支持的操作系统版本
支持以下操作系统版本：

- Windows 8/Windows 8.1
- Windows Server 2012 R2
- Windows 10

### Visual Studio 2015

适用于 Service Fabric 的工具取决于 Visual Studio 2015，你可以在 [Visual Studio 网站][2]上找到。

> [AZURE.NOTE] 如果未运行其中一个支持的 OS 版本或不想在计算机上安装 Visual Studio 2015，则可以在预安装 Windows Server 2012 R2 和 Visual Studio 2015 的情况下设置 Azure 虚拟机。你可以使用 Azure 虚拟机库中的映像来执行这项操作。

## 安装运行时、SDK 和工具

Web 平台安装程序会执行 Service Fabric 组件的安装。请按照这些说明安装：

1. 使用 Web 平台安装程序[下载 SDK][3]。

2. 单击“安装”开始安装过程。

3. 查看并接受 EULA。

将自动继续进行安装。

## 启用 PowerShell 脚本执行

Service Fabric 使用 Windows PowerShell 脚本创建本地开发群集和部署 Visual Studio 中的应用程序。默认情况下，Windows 会阻止这些脚本运行。若要启用它们，你必须修改你的 PowerShell 执行策略。以管理员身份打开 PowerShell 并输入以下命令：

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## 后续步骤
设置开发环境之后，你可以开始构建和运行应用。

- [在 Visual Studio 中创建你的第一个 Service Fabric 应用程序](/documentation/articles/service-fabric-create-your-first-application-in-visual-studio)
- [了解如何在本地群集上部署和管理应用程序](/documentation/articles/service-fabric-get-started-with-a-local-cluster)
- [了解编程模型：Reliable Services 和 Reliable Actors](/documentation/articles/service-fabric-choose-framework)

- [使用 Service Fabric 资源管理器可视化群集](/documentation/articles/service-fabric-visualizing-your-cluster)

[1]: /documentation/services/service-fabric "Service Fabric 活动页"
[2]: https://www.visualstudio.com/downloads/download-visual-studio-vs "VS RC"
[3]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "WebPI 链接"

<!---HONumber=Mooncake_0307_2016-->