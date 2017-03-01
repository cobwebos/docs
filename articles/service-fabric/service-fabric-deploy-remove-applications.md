---
title: "Service Fabric 应用程序部署 | Microsoft 文档"
description: "如何在 Service Fabric 中部署和删除应用程序"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/16/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 6626747c501b01aa5e53657309ec79c75213483c
ms.openlocfilehash: 2e96e978c56ef2b9c901c952a6e96055a6ab91cf


---
# <a name="deploy-and-remove-applications-using-powershell"></a>部署和删除使用 PowerShell 的应用程序
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> 
> 

<br/>

[打包应用程序类型][10]后，即可部署到 Azure Service Fabric 群集中。 部署涉及以下三个步骤：

1. 上载应用程序包
2. 注册应用程序类型
3. 创建应用程序实例

> [!NOTE]
> 如果使用 Visual Studio 来部署和调试本地开发群集上的应用程序，则将通过 PowerShell 脚本自动处理以下所有步骤。  可在应用程序项目的脚本文件夹中找到此脚本。 本文提供有关这些脚本正在执行什么操作的背景，以便你可以在 Visual Studio 外部执行相同的操作。
> 
> 

## <a name="upload-the-application-package"></a>上载应用程序包
上载应用程序包会将其放在一个可由内部 Service Fabric 组件访问的位置。 你可以使用 PowerShell 执行上载。 在运行本文中的任何 PowerShell 命令之前，请始终先使用 [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/servicefabric/vlatest/connect-servicefabriccluster) 连接到 Service Fabric 群集。

假设你有一个名为 *MyApplicationType* 的文件夹，其中包含必要的应用程序清单、服务清单以及代码/配置/数据包。 [Copy-ServiceFabricApplicationPackage](https://docs.microsoft.com/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) 命令可将包上传到群集映像存储。 Service Fabric SDK PowerShell 模块中包含的 **Get-ImageStoreConnectionStringFromClusterManifest** cmdlet，用于获取映像存储连接字符串。  要导入 SDK 模块，请运行：

```
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

可以将应用程序包从 *C:\users\ryanwi\Documents\Visual Studio 2015\Projects\MyApplication\myapplication\pkg\debug* 复制到 *c:\temp\MyApplicationType*（将“debug”目录重命名为“MyApplicationType”）。 以下示例将上载包：

```powershell
PS C:\temp> dir

    Directory: c:\temp


Mode                LastWriteTime         Length Name                                                                                   
----                -------------         ------ ----                                                                                   
d-----        8/12/2016  10:23 AM                MyApplicationType                                                                          

PS C:\temp> tree /f .\Stateless1Pkg

C:\TEMP\MyApplicationType
│   ApplicationManifest.xml
|
└───Stateless1Pkg
    |   ServiceManifest.xml
    │
    └───Code
    │   │  Microsoft.ServiceFabric.Data.dll
    │   │  Microsoft.ServiceFabric.Data.Interfaces.dll
    │   │  Microsoft.ServiceFabric.Internal.dll
    │   │  Microsoft.ServiceFabric.Internal.Strings.dll
    │   │  Microsoft.ServiceFabric.Services.dll
    │   │  ServiceFabricServiceModel.dll
    │   │  MyService.exe
    │   │  MyService.exe.config
    │   │  MyService.pdb
    │   │  System.Fabric.dll
    │   │  System.Fabric.Strings.dll
    │   │
    │   └───en-us
    |         Microsoft.ServiceFabric.Internal.Strings.resources.dll
    |         System.Fabric.Strings.resources.dll
    |
    ├───Config
    │     Settings.xml
    │
    └───Data
          init.dat

PS C:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath MyApplicationType -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
Copy application package succeeded

PS D:\temp>
```

请参阅[了解映像存储连接字符串](service-fabric-image-store-connection-string.md)，获取有关映像存储和 ImageStoreConnectionString 的补充信息。

## <a name="register-the-application-package"></a>注册应用程序包
应用程序清单中声明的应用程序类型和版本会在注册应用包时可供使用。 系统将读取上一步中上传的程序包，验证此包，处理包的内容，并将已处理的包复制到内部系统位置。  如果要在本地验证应用包，请使用 [Test-ServiceFabricApplicationPackage](https://docs.microsoft.com/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) cmdlet。

```powershell
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
```

[Register-ServiceFabricApplicationType](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) 命令将仅在系统成功复制应用程序包后返回。 注册花费的时间取决于应用程序包的大小和内容。 如果需要，**-TimeoutSec** 参数可用于提供更长的超时（默认超时为 60 秒）。  如果这是大型应用包，并且你遇到超时，请使用 **-Async** 参数。

[Get-ServiceFabricApplicationType](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) 命令将列出已成功注册的所有应用程序类型版本。

## <a name="create-the-application"></a>创建应用程序
可以使用已通过 [New-ServiceFabricApplication](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricapplication) 命令成功注册的任何应用程序类型版本来实例化应用程序。 每个应用程序的名称必须以 *fabric:* 方案开头，并且对每个应用程序实例是唯一的。 还会创建目标应用程序类型的应用程序清单中定义的任何默认服务。

```powershell
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
```

[Get-ServiceFabricApplication](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplication) 命令将列出已成功创建的所有应用程序实例以及其整体状态。

[Get-ServiceFabricService](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricservice) 命令将列出在给定的应用程序实例中成功创建的所有服务实例。 在此处列出默认服务（如果有）。

可以为已注册应用程序类型的任何给定版本创建多个应用程序实例。 每个应用程序实例都将隔离运行，具有其自己的工作目录和进程。

## <a name="remove-an-application"></a>删除应用程序
不再需要某个应用程序实例时，可以使用 [Remove-ServiceFabricApplication](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricapplication) 命令将其永久删除。 此命令也将自动删除属于该应用程序的所有服务，永久删除所有服务状态。 此操作无法撤消，并且无法恢复应用程序状态。

```powershell
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
```

不再需要应用程序类型的某个特定版本时，应使用 [Unregister-ServiceFabricApplicationType](https://docs.microsoft.com/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) 命令将其注销。 注销未使用的类型将在映像存储中释放该类型的应用程序包内容所使用的存储空间。 只要没有针对其实例化的应用程序或引用它的挂起应用程序升级，就可以注销应用程序类型。

```powershell
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
```

## <a name="troubleshooting"></a>故障排除
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage 请求 ImageStoreConnectionString
Service Fabric SDK 环境应该已经设置了正确的默认设置。 若有需要，所有命令的 ImageStoreConnectionString 都应匹配 Service Fabric 群集正在使用的值。 可以在使用 [Get-ServiceFabricClusterManifest](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricclustermanifest) 命令检索到的群集清单中找到 ImageStoreConnectionString：

```powershell
PS D:\temp> Get-ServiceFabricClusterManifest
```

ImageStoreConnectionString 可在群集清单中找到：

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

## <a name="next-steps"></a>后续步骤
[Service Fabric 应用程序升级](service-fabric-application-upgrade.md)

[Service Fabric 运行状况简介](service-fabric-health-introduction.md)

[对 Service Fabric 进行诊断和故障排除](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[在 Service Fabric 中对应用程序建模](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md



<!--HONumber=Feb17_HO2-->


