---
title: "打包 Azure Service Fabric 应用 | Microsoft Docs"
description: "如何打包 Service Fabric 应用程序后将其部署到群集。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: ryanwi
ms.openlocfilehash: 0db49b9bd50c640175292671f813d23d960b52e1
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="package-an-application"></a>打包应用程序
本文介绍如何打包 Service Fabric 应用程序并为部署做好准备。

## <a name="package-layout"></a>包布局
必须将应用程序清单、一个或多个服务清单和其他必要的包文件组织为一个特定的布局，以部署到 Service Fabric 群集中。 本文中的示例清单需要组织为以下目录结构：

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

文件夹的名称与每个相应元素的 **Name** 特性匹配。 例如，如果服务清单包含两个名为 **MyCodeA** 和 **MyCodeB** 的代码包，则两个同名的文件夹将包含用于每个代码包的必要二进制文件。

## <a name="use-setupentrypoint"></a>使用 SetupEntryPoint
**SetupEntryPoint** 的典型使用场景是需要在服务启动之前运行可执行文件，或需要使用提升的权限来执行操作时。 例如：

* 设置和初始化服务可执行文件所需的环境变量。 这并不仅限于通过 Service Fabric 编程模型编写的可执行文件。 例如，npm.exe 需要配置一些环境变量来部署 node.js 应用程序。
* 通过安装安全证书设置访问控制。

有关如何配置 **SetupEntryPoint** 的详细信息，请参阅[配置服务设置入口点的策略](service-fabric-application-runas-security.md)

<a id="Package-App"></a>
## <a name="configure"></a>配置
### <a name="build-a-package-by-using-visual-studio"></a>使用 Visual Studio 生成包
如果使用 Visual Studio 2015 创建应用程序，可以使用“包”命令自动创建符合上述布局的包。

若要创建包，请在“解决方案资源管理器”中右键单击应用程序项目，并选择“包”命令，如下所示：

![使用 Visual Studio 打包应用程序][vs-package-command]

打包完成后，该包的位置会显示在“输出”窗口中。 在 Visual Studio 中部署或调试应用程序时，打包步骤自动发生。

### <a name="build-a-package-by-command-line"></a>通过命令行生成一个包
还可以使用 `msbuild.exe` 以编程方式打包应用程序。 这是 Visual Studio 实际运行的操作，因此输出是相同的。

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>测试包
可以使用 [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) 命令，通过 PowerShell 在本地验证包结构。
此命令会检查是否存在清单分析问题，并验证所有引用。 此命令只验证包中目录与文件结构的正确性。
它不验证任何代码或数据包内容，而只检查所有必要的文件是否存在。

```
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

此错误显示代码包中缺少服务清单 **SetupEntryPoint** 中引用的 *MySetup.bat* 文件。 添加缺少的文件后，应用程序验证通过：

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
```

如果应用程序已定义[应用程序参数](service-fabric-manage-multiple-environment-app-configuration.md)，则可以将参数传递到 [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) 中进行适当验证。

如果知道要在其中部署应用程序的群集，则建议传入 `ImageStoreConnectionString` 参数。 在这种情况下，还需要针对已在群集中运行的前一应用程序版本对包进行验证。 例如，验证可检测是否部署了版本相同但内容不同的包。  

在应用程序正确打包并通过验证后，可以考虑将包进行压缩以便更快地执行部署操作。

## <a name="compress-a-package"></a>压缩包
包较大或包含大量文件时，可压缩该包以提高部署速度。 压缩可以减少文件的数量和包大小。
与上传未压缩的包相比，对于压缩的应用程序包，[上传应用程序包](service-fabric-deploy-remove-applications.md#upload-the-application-package)可能要花费更长的时间，尤其是当压缩是在复制过程中执行的时。 使用压缩，[注册](service-fabric-deploy-remove-applications.md#register-the-application-package)和[注销应用程序类型](service-fabric-deploy-remove-applications.md#unregister-an-application-type)时速度会更快。

压缩包和未压缩包的部署机制相同。 如果为压缩包，则存储在群集映像存储等位置，并且在应用程序运行前在节点上解压缩。
压缩会将有效的 Service Fabric 包替换为已压缩版本。 文件夹必须允许写入操作。 对已压缩的包运行压缩将不会产生任何更改。

可以通过使用 `CompressPackage` 开关运行 Powershell 命令 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 来压缩包。 可以通过使用 `UncompressPackage` 开关运行同一命令来解压缩包。

以下命令可压缩包，但不会将包复制到映像存储区。 通过使用不带 `SkipCopy` 标志的 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 将压缩的包复制到一个或多个 Service Fabric 群集中。
包中现在包括 `code`、`config` 和 `data` 包的压缩文件。 应用程序清单和服务清单不会被压缩，因为它们是许多内部操作所必需的。 例如，进行某些验证时的包共享、应用程序类型名称和版本提取都需要访问清单。 压缩清单会使这些操作无效。

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy

PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

或者，也可以使用 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 一步压缩并复制包。
如果包较大，请提供足够的超时时间，以为包压缩和上传到群集预留时间。
```
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

在内部，Service Fabric 将计算应用程序包的校验和以进行验证。 使用压缩时，将对每个包的压缩版本计算校验和。 基于同一个应用程序包生成新 zip 会创建不同的校验和。 若要防止验证错误，请使用[差异预配](service-fabric-application-upgrade-advanced.md)。 使用此选项时，不要在新版本中包括未更改的包。 相反，请直接从新的服务清单中引用它们。

如果差异预配不是一个选项并且你必须包括这些包，请为 `code`、`config` 和 `data` 包生成新版本以避免校验和不匹配。 当使用压缩的包时，无论以前的版本是否使用压缩，都需要为未更改的包生成新版本。

现在已将包进行了正确打包、验证和压缩（如需要），因此可以将其[部署](service-fabric-deploy-remove-applications.md)到一个或多个 Service Fabric 群集。

### <a name="compress-packages-when-deploying-using-visual-studio"></a>使用 Visual Studio 部署时压缩程序包
可以指示 Visual Studio 在部署时压缩程序包，具体方法为将 `CopyPackageParameters` 元素添加到发布配置文件，并将 `CompressPackage` 属性设置为 `true`。

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>创建 sfpkg
从版本 6.1 开始，Service Fabric 允许从外部存储区进行预配。
使用此选项时，不必将应用程序包复制到映像存储区。 相反，你可以创建一个 `sfpkg` 并将其上传到外部存储区，然后在预配时将下载 URI 提供给 Service Fabric。 可以将同一个包预配给多个群集。 从外部存储区进行预配会节省将包复制到每个群集所需的时间。

`sfpkg` 文件是一个 zip 文件，其中包含初始应用程序包并具有扩展名“.sfpkg”。
在此 zip 文件内，应用程序包可以是压缩的，也可以是未压缩的。 如[前文所述](service-fabric-package-apps.md#compress-a-package)，zip 文件内应用程序包的压缩是在代码、配置和数据包级别执行的。

若要创建 `sfpkg`，请首先创建包含原始应用程序包（压缩的或未压缩的）的文件夹。 然后，使用任何实用程序以扩展名“.sfpkg”压缩该文件夹。 例如，使用 [ZipFile.CreateFromDirectory](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx)。

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

必须在带外将 `sfpkg` 上传到 Service Fabric 外部的外部存储区。 外部存储区可以是公开了 REST http 或 https 终结点的任何存储区。 在预配期间，Service Fabric 执行 GET 操作来下载 `sfpkg` 应用程序包，因此该存储区必须允许对该包进行读取访问。

若要预配该包，请使用外部预配，这需要使用下载 URI 和应用程序类型信息。

>[!NOTE]
> 基于映像存储区相对路径进行预配当前不支持 `sfpkg` 文件。 因此，不应当将 `sfpkg` 复制到映像存储区。

## <a name="next-steps"></a>后续步骤
[部署和删除应用程序][10]介绍如何使用 PowerShell 来管理应用程序实例

[管理多个环境的应用程序参数][11]介绍如何配置不同应用程序实例的参数和环境变量。

[配置应用程序的安全策略][12]介绍如何在安全策略下运行服务，从而对访问进行限制。

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
