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
ms.date: 02/23/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: dbd4dd3cadf162ea18d58639d31589f7b9b8efc3
ms.openlocfilehash: 2dfdcd08501a63d62ec6ba565d1abc7d42c8c680
ms.lasthandoff: 02/27/2017


---
# <a name="deploy-and-remove-applications-using-powershell"></a>部署和删除使用 PowerShell 的应用程序
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> 
> 

<br/>

[打包应用程序类型][10]后，即可部署到 Azure Service Fabric 群集中。 部署涉及以下三个步骤：

1. 将应用程序包上载到映像存储
2. 注册应用程序类型
3. 创建应用程序实例

在部署应用并且实例在群集中运行后，你可以删除应用实例及其应用程序类型。 从群集中完全删除某个应用涉及以下步骤：

1. 删除正在运行的应用程序实例
2. 如果不再需要该应用程序类型，则将其取消注册
3. 从映像存储中删除应用程序包

如果使用 Visual Studio 来部署和调试本地开发群集上的应用程序，则将通过 PowerShell 脚本自动处理上述所有步骤。[](service-fabric-publish-app-remote-cluster.md)  可在应用程序项目的 *Scripts* 文件夹中找到此脚本。 本文提供了有关这些脚本正在执行什么操作的背景，以便你可以在 Visual Studio 外部执行相同的操作。 
 
## <a name="connect-to-the-cluster"></a>连接至群集
在运行本文中的任何 PowerShell 命令之前，请始终先使用 [Connect-ServiceFabricCluster](/powershell/servicefabric/vlatest/connect-servicefabriccluster) 连接到 Service Fabric 群集。 若要连接到本地部署群集，请运行以下命令：

```powershell
PS C:\>Connect-ServiceFabricCluster
```

有关连接到远程群集或连接到使用 Azure Active Directory、X509 证书或 Windows Active Directory 保护的群集的示例，请参阅[连接到安全群集](service-fabric-connect-to-secure-cluster.md)。

## <a name="upload-the-application-package"></a>上载应用程序包
上载应用程序包会将其放在一个可由内部 Service Fabric 组件访问的位置。 如果要在本地验证应用包，请使用 [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) cmdlet。  [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) 命令用来将应用程序包上载到群集映像存储。 Service Fabric SDK PowerShell 模块中包含的 **Get-ImageStoreConnectionStringFromClusterManifest** cmdlet，用于获取映像存储连接字符串。  要导入 SDK 模块，请运行：

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

假设你在 Visual Studio 中生成并打包名为 *MyApplication* 的应用。 默认情况下，ApplicationManifest.xml 中列出的应用程序类型名称为“MyApplicationType”。  应用程序包（其中包含必需的应用程序清单、服务清单以及代码/配置/数据包）位于 *C:\Users\username\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug* 中。 

以下命令列出应用程序包的内容：

```powershell
PS C:\> tree /f 'C:\Users\user\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

以下示例将包上载到映像存储中名为“MyApplicationV1”的文件夹中：

```powershell
PS C:\> $path = 'C:\Users\user\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
```

如果未指定 *-ApplicationPackagePathInImageStore* 参数，则应用包将复制到映像存储中的“Debug”文件夹。

有关映像存储和映像存储连接字符串的补充信息，请参阅[了解映像存储连接字符串](service-fabric-image-store-connection-string.md)。

## <a name="register-the-application-package"></a>注册应用程序包
应用程序清单中声明的应用程序类型和版本会在注册应用包时可供使用。 系统将读取上一步中上载的程序包，验证此包，处理包的内容，并将已处理的包复制到内部系统位置。  

运行 [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) cmdlet 以在群集中注册应用程序类型并使其可用于部署：

```powershell
PS C:\> Register-ServiceFabricApplicationType MyApplicationV1
Register application type succeeded
```

“MyApplicationV1”是映像存储中应用包所在的文件夹。 现在已在群集中注册了名为“MyApplicationType”且版本为“1.0.0”（两者都可以在应用程序清单中找到）的应用程序类型。

[Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) 命令只有在系统成功注册应用程序包后才会返回。 注册花费的时间取决于应用程序包的大小和内容。 如果需要，**-TimeoutSec** 参数可用于提供更长的超时（默认超时为 60 秒）。  如果这是大型应用包，并且你遇到超时，请使用 **-Async** 参数。

[Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) 命令将列出已成功注册的所有应用程序类型版本及其注册状态。

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="create-the-application"></a>创建应用程序
可以使用 [New-ServiceFabricApplication](/powershell/servicefabric/vlatest/new-servicefabricapplication) cmdlet 通过已成功注册的任何应用程序类型版本来实例化应用程序。 每个应用程序的名称必须以 *fabric:* 方案开头，并且对每个应用程序实例是唯一的。 还会创建目标应用程序类型的应用程序清单中定义的任何默认服务。

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
可以为已注册应用程序类型的任何给定版本创建多个应用程序实例。 每个应用程序实例都将隔离运行，具有其自己的工作目录和进程。

若要查看有哪些已命名应用和服务正在群集中运行，请运行 [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) 和 [Get-ServiceFabricService](/powershell/servicefabric/vlatest/get-servicefabricservice) cmdlet：

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>删除应用程序
当不再需要某个应用程序实例时，可以使用 [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication) cmdlet 通过指定其名称将其删除。 [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication) 还将自动删除属于该应用程序的所有服务，永久删除所有服务状态。 此操作无法撤消，并且无法恢复应用程序状态。

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>取消注册应用程序类型
当不再需要应用程序类型的某个特定版本时，应使用 [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) cmdlet 取消注册该应用程序类型。 取消注册未使用的应用程序类型将释放映像存储使用的存储空间。 只要没有针对其实例化的应用程序或引用它的挂起应用程序升级，就可以注销应用程序类型。

若要查看群集中当前已注册的应用程序类型，请运行 [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype)：

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

运行 [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) 来取消注册特定的应用程序类型：

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```
## <a name="remove-an-application-package-from-the-image-store"></a>从映像存储中删除应用程序包
当不再需要某个应用程序包时，可以将其从映像存储中删除以释放系统资源。

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
```

## <a name="troubleshooting"></a>故障排除
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage 请求 ImageStoreConnectionString
Service Fabric SDK 环境应该已经设置了正确的默认设置。 若有需要，所有命令的 ImageStoreConnectionString 都应匹配 Service Fabric 群集正在使用的值。 可以在使用 [Get-ServiceFabricClusterManifest](/powershell/servicefabric/vlatest/get-servicefabricclustermanifest) 命令检索到的群集清单中找到 ImageStoreConnectionString：

```powershell
PS C:\> Get-ServiceFabricClusterManifest
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

