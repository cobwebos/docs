---
title: "Azure Service Fabric 应用程序部署 | Microsoft Docs"
description: "如何在 Service Fabric 中使用 PowerShell 部署和删除应用程序。"
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
ms.date: 10/05/2017
ms.author: ryanwi
ms.openlocfilehash: f19141919b3c61123e0e94c4513f872e095620c1
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="deploy-and-remove-applications-using-powershell"></a>部署和删除使用 PowerShell 的应用程序
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient API](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

[打包应用程序类型][10]后，即可部署到 Azure Service Fabric 群集中。 部署涉及以下三个步骤：

1. 将应用程序包上传到映像存储
2. 注册应用程序类型
3. 创建应用程序实例

在部署应用程序并且实例在群集中运行后，可以删除应用程序实例及其应用程序类型。 从群集中完全删除某个应用程序涉及以下步骤：

1. 删除正在运行的应用程序实例
2. 如果不再需要该应用程序类型，则将其取消注册
3. 从映像存储中删除应用程序包

如果使用 Visual Studio 来部署和调试本地开发群集上的应用程序，则将通过 PowerShell 脚本自动处理上述所有步骤。  可在应用程序项目的 *Scripts* 文件夹中找到此脚本。 本文提供了有关这些脚本正在执行什么操作的背景，以便可以在 Visual Studio 外部执行相同的操作。 
 
## <a name="connect-to-the-cluster"></a>连接至群集
在运行本文中的任何 PowerShell 命令之前，请始终先使用 [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) 连接到 Service Fabric 群集。 若要连接到本地部署群集，请运行以下命令：

```powershell
PS C:\>Connect-ServiceFabricCluster
```

有关连接到远程群集或连接到使用 Azure Active Directory、X509 证书或 Windows Active Directory 保护的群集的示例，请参阅[连接到安全群集](service-fabric-connect-to-secure-cluster.md)。

## <a name="upload-the-application-package"></a>上传应用程序包
上传应用程序包会将其放在一个可由内部 Service Fabric 组件访问的位置。
如果要在本地验证应用程序包，请使用 [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) cmdlet。

[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 命令用来将应用程序包上传到群集映像存储。

假设在 Visual Studio 2015 中生成并打包名为 *MyApplication* 的应用程序。 默认情况下，ApplicationManifest.xml 中列出的应用程序类型名称为“MyApplicationType”。  应用程序包（其中包含必需的应用程序清单、服务清单以及代码/配置/数据包）位于 *C:\Users\<username\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug* 中。 

以下命令列出应用程序包的内容：

```powershell
PS C:\> $path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
PS C:\> tree /f $path
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

如果应用程序包很大，并且/或者包含许多文件，可以[进行压缩](service-fabric-package-apps.md#compress-a-package)。 压缩可以减小文件大小，减少文件数量。
附带作用是加快注册和注销应用程序类型。 上传时间目前可能会降低，尤其是当包含压缩包的时间时。 

若要压缩包，请使用同一 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 命令。 可以使用 `SkipCopy` 标记独立于上传进行压缩，也可以与上传操作配合使用进行压缩。 对压缩包应用压缩不会执行任何操作。
要解压缩压缩包，请将同一 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 命令与 `UncompressPackage` 切换配合使用。

以下 cmdlet 可压缩包，而不会将包复制到映像存储区。 此包中现在包括 `Code` 包和 `Config` 包的压缩文件。 不会压缩应用程序和服务清单，因为许多内部操作均需要使用它们（例如，包共享、应用程序类型名称和某些验证的版本提取）。 压缩清单会使这些操作无效。

```
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

对于大型应用程序包，压缩操作将耗费时间。 为获得最佳结果，请使用快速 SSD 驱动器。 压缩时间和压缩包的大小也会因包内容而有所不同。
例如，以下是某些包的压缩统计信息，其中显示初始大小和压缩包大小，及压缩时间。

|初始大小 (MB)|文件计数|压缩时间|压缩包大小 (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

对包进行压缩后，便可根据需要将其上传到一个或多个 Service Fabric 群集。 压缩包和未压缩包的部署机制相同。 如果为压缩包，则存储在群集映像存储等位置，并且在应用程序运行前在节点上解压缩。


以下示例将包上传到映像存储中名为“MyApplicationV1”的文件夹中：

```powershell
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

如果未指定 *-ApplicationPackagePathInImageStore* 参数，则应用程序包将复制到映像存储中的“Debug”文件夹。

>[!NOTE]
>如果 PowerShell 会话已连接到 Service Fabric 群集，Copy-ServiceFabricApplicationPackage 会自动检测相应的映像存储区连接字符串。 对于版本低于 5.6 的 Service Fabric，必须显式提供 -ImageStoreConnectionString 参数。
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>Service Fabric SDK PowerShell 模块中包含的 **Get-ImageStoreConnectionStringFromClusterManifest** cmdlet，用于获取映像存储连接字符串。  要导入 SDK 模块，请运行：
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>有关映像存储和映像存储连接字符串的补充信息，请参阅[了解映像存储连接字符串](service-fabric-image-store-connection-string.md)。
>
>
>

上传包所费时间取决于多种因素。 其中一些因素是包中文件数、包大小和文件大小。 源计算机与 Service Fabric 群集之间的网络速度也会影响上传时间。 默认 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 的超时为 30 分钟。
根据上述因素，可能需要增加超时。 如果在复制调用中对包进行压缩，还需要考虑压缩时间。



## <a name="register-the-application-package"></a>注册应用程序包
应用程序清单中声明的应用程序类型和版本会在注册应用程序包时可供使用。 系统将读取上一步中上传的程序包，验证此包，处理包的内容，并将已处理的包复制到内部系统位置。  

运行 [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) cmdlet 以在群集中注册应用程序类型并使其可用于部署：

```powershell
PS C:\> Register-ServiceFabricApplicationType MyApplicationV1
Register application type succeeded
```

“MyApplicationV1”是映像存储中应用程序包所在的文件夹。 现在已在群集中注册了名为“MyApplicationType”且版本为“1.0.0”（两者都可以在应用程序清单中找到）的应用程序类型。

[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 命令只有在系统成功注册应用程序包后才会返回。 注册花费的时间取决于应用程序包的大小和内容。 如果需要，**-TimeoutSec** 参数可用于提供更长的超时（默认超时为 60 秒）。

如果在处理大型应用程序包，或者遇到超时，请使用 **-Async** 参数。 该命令会在群集接受注册命令时返回，并根据需要继续进行处理。
[Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) 命令将列出已成功注册的所有应用程序类型版本及其注册状态。 此命令可用于确定注册的完成时间。

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>从映像存储中删除应用程序包
建议在成功注册应用程序后删除应用程序包。  从映像存储区中删除应用程序包可以释放系统资源。  保留未使用的应用程序包会占用磁盘存储空间，导致应用程序出现性能问题。

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>创建应用程序
可以使用 [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet 通过已成功注册的任何应用程序类型版本来实例化应用程序。 每个应用程序的名称必须以“fabric:”方案开头，并且必须对每个应用程序实例是唯一的。 还会创建目标应用程序类型的应用程序清单中定义的任何默认服务。

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
可以为已注册应用程序类型的任何给定版本创建多个应用程序实例。 每个应用程序实例都将隔离运行，具有其自己的工作目录和进程。

若要查看有哪些已命名应用和服务正在群集中运行，请运行 [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) 和 [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) cmdlet：

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
当不再需要某个应用程序实例时，可以使用 [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) cmdlet 通过指定其名称将其删除。 [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) 还会自动删除属于该应用程序的所有服务，永久删除所有服务状态。 

> [!WARNING]
> 此操作无法撤消，并且无法恢复应用程序状态。

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>取消注册应用程序类型
当不再需要某个特定版本的应用程序类型时，应使用 [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) cmdlet 取消注册该应用程序类型。 通过删除应用程序二进制文件，取消注册未使用的应用程序类型将释放映像存储使用的存储空间。 取消注册应用程序类型不会删除应用程序包。 只要没有针对其实例化的应用程序或引用它的挂起应用程序升级，就可以注销应用程序类型。

若要查看群集中当前已注册的应用程序类型，请运行 [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps)：

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

运行 [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) 来取消注册特定的应用程序类型：

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>故障排除
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage 请求 ImageStoreConnectionString
Service Fabric SDK 环境应该已经设置了正确的默认设置。 若有需要，所有命令的 ImageStoreConnectionString 都应匹配 Service Fabric 群集正在使用的值。 可以在使用 [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) 和 Get-ImageStoreConnectionStringFromClusterManifest 命令检索到的群集清单中找到 ImageStoreConnectionString：

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

Service Fabric SDK PowerShell 模块中包含的 **Get-ImageStoreConnectionStringFromClusterManifest** cmdlet，用于获取映像存储连接字符串。  要导入 SDK 模块，请运行：

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
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

有关映像存储和映像存储连接字符串的补充信息，请参阅[了解映像存储连接字符串](service-fabric-image-store-connection-string.md)。

### <a name="deploy-large-application-package"></a>部署大型应用程序包
问题：大型应用程序包（GB 级别）的 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 超时。
请尝试：
- 通过 `TimeoutSec` 参数为 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 命令指定更长的超时。 此超时默认为 30 分钟。
- 检查源计算机和群集之间的网络连接。 如果连接缓慢，请考虑使用一台网络连接状况更好的计算机。
如果客户端计算机位于另一个区域，而不在此群集中，请考虑使用此群集的邻近区域或同区域中的客户端计算机。
- 检查是否已达到外部限制。 例如，将映像存储配置为使用 Azure 存储时，可能会限制上传。

问题：已成功完成上传包，但 [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 超时。请尝试：
- 复制到映像存储之前[对包进行压缩](service-fabric-package-apps.md#compress-a-package)。
压缩可减小文件大小，减少文件数量，这反过来会减少通信流量和 Service Fabric 必须执行的工作量。 上传操作可能会变慢（尤其是包括压缩时间时），但注册和注销应用程序类型会加快。
- 通过 `TimeoutSec` 参数为 [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 指定更长的超时。
- 为 [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 指定 `Async` 切换。 当群集接受命令并且应用程序类型的注册以异步方式继续时，该命令将返回。 因此，在此情况下，无需指定较长的超时。 [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) 命令将列出已成功注册的所有应用程序类型版本及其注册状态。 此命令可用于确定注册的完成时间。

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>部署包含多个文件的应用程序包
问题：具有多个文件（上千个）的应用程序包的 [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 超时。
请尝试：
- 复制到映像存储之前[对包进行压缩](service-fabric-package-apps.md#compress-a-package)。 压缩可以减少文件数量。
- 通过 `TimeoutSec` 参数为 [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 指定更长的超时。
- 为 [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 指定 `Async` 切换。 当群集接受命令并且应用程序类型的注册以异步方式继续时，该命令将返回。
因此，在此情况下，无需指定较长的超时。 [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) 命令将列出已成功注册的所有应用程序类型版本及其注册状态。 此命令可用于确定注册的完成时间。

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>后续步骤
[Service Fabric 应用程序升级](service-fabric-application-upgrade.md)

[Service Fabric 运行状况简介](service-fabric-health-introduction.md)

[对 Service Fabric 进行诊断和故障排除](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[在 Service Fabric 中对应用程序建模](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
