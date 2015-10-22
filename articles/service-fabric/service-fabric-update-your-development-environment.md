<properties
   pageTitle="更新你的 Service Fabric 开发环境 | Microsoft Azure"
   description="更新你的 Service Fabric 开发环境以使用最新的运行时、SDK 和工具。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="07/29/2015"
   wacn.date=""/>

# 更新你的 Service Fabric 开发环境

 Service Fabric 定期提供用于在本地开发中使用的新版本运行时、SDK 和工具。使用这些新版本更新本地开发环境以确保在本地构建和测试你的应用程序时始终具有最新的功能、bug 修复及性能改进。

## 清理你的本地群集

 Service Fabric 目前不支持在本地群集正在运行的情况下升级 Service Fabric 运行时。为了确保干净升级，首先清理你的本地群集非常重要。

 >[AZURE.NOTE]清理你的本地群集将删除所有已部署应用程序及其数据。

 可以按如下所示清理你的本地群集：


 1. 关闭所有其他 PowerShell 窗口并以管理员身份启动一个新窗口。

 2. 使用 `cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"` 导航到群集安装目录

 3. 运行 `.\CleanCluster.ps1`


## 更新运行时、SDK 和工具

 一旦成功清理了你的现有群集，则可以按如下所示继续升级：


 1. 启动 Web 平台安装程序以[更新到新版本][1]。

 2. 完成后，以管理员身份立即启动一个 PowerShell 窗口，并使用 `cd "$ENV:ProgramFiles\Microsoft SDKs\ServiceFabric\ClusterSetup"` 导航到群集安装目录。

 3. 运行 `.\DevClusterSetup.ps1` 设置你的本地群集。

就这么简单！ 现在，你可以启动 Visual Studio 并继续构建 Service Fabric 应用程序。

>[AZURE.NOTE]在此版本中，默认项目结构已经改变。你应该能够在 Visual Studio 中打开和运行现有项目。然而，如果你在构建、部署或调试应用程序时遇到任何问题，请考虑创建一个新的项目并迁移你的代码。

 [1]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "WebPI 链接"

<!---HONumber=74-->