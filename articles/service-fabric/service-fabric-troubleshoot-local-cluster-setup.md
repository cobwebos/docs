<properties
   pageTitle="排除本地群集安装的故障"
   description="本文就本地开发群集的故障介绍一些建议"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="07/09/2015"
   wacn.date=""/>

# 排除本地开发群集安装的故障

如果你在与本地开发群集交互时遇到问题，请查看以下建议以获得可能的解决方案。

## 群集安装失败

### 无法清除 Service Fabric 日志

#### 问题

在运行 DevClusterSetup 脚本时，你看到了类似下面的错误：

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### 解决方案

关闭当前 Powershell 窗口，然后以管理员身份启动一个新的 Powershell 窗口。现在应能成功运行该脚本。

## 群集连接失败

### 类型初始化异常

#### 问题

当在 PowerShell 或 Service Fabric 资源管理器中连接到群集时，你看到了针对 System.Fabric.Common.AppTrac 的 TypeInitializationException 错误。

#### 解决方案

在安装期间未正确设置路径变量。请注销 Windows 然后重新登录。这将完全刷新你的路径。

### 群集连接失败，并显示“对象已关闭”

#### 问题

对 Connect-ServiceFabricCluster 的调用失败，并显示类似下面的错误：

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### 解决方案

关闭当前 Powershell 窗口，然后以管理员身份启动一个新的 Powershell 窗口。现在应能成功连接。

### FabricConnectionDeniedException

#### 问题

当从 Visual Studio 调试时，系统出现 FabricConnectionDeniedException 错误。

#### 解决方案

此错误通常在你尝试手动启动一个服务主机进程（而不是允许 Service Fabric 运行时为你启动该进程）时出现。

确保在你的解决方案中没有将任何服务项目设置为启动项目。只应将 Service Fabric 应用程序项目设置为启动项目。


## 后续步骤

- [使用系统运行状况报告了解群集并排除故障](/documentation/articles/service-fabric-understand-and-troubleshoot-with-system-health-reports)
- [使用 Service Fabric 资源管理器可视化群集](/documentation/articles/service-fabric-visualizing-your-cluster)

<!---HONumber=74-->