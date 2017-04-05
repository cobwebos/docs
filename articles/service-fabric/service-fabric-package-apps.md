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
ms.date: 3/24/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 45bf19b4c8406cfc09624bef2b9c0f1c443d8fd6
ms.lasthandoff: 03/27/2017


---
# <a name="package-an-application"></a>打包应用程序
本文介绍如何打包 Service Fabric 应用程序并为部署做好准备。

## <a name="package-layout"></a>包布局
必须将应用程序清单、服务清单和其他必要的包文件组织为一个特定的布局，以部署到 Service Fabric 群集中。 本文中的示例清单需要组织为以下目录结构：

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

* 设置和初始化服务可执行文件所需的环境变量。 这并不限于通过 Service Fabric 编程模型编写的可执行文件。 例如，npm.exe 需要配置一些环境变量来部署 node.js 应用程序。
* 通过安装安全证书设置访问控制。

有关如何配置 **SetupEntryPoint** 的详细信息，请参阅[配置服务设置入口点的策略](service-fabric-application-runas-security.md)  

## <a name="configure"></a>配置 
### <a name="build-a-package-by-using-visual-studio"></a>使用 Visual Studio 生成包
如果使用 Visual Studio 2015 创建应用程序，你可以使用“包”命令自动创建符合上述布局的包。

若要创建包，请在“解决方案资源管理器”中右键单击应用程序项目，并选择“包”命令，如下所示：

![使用 Visual Studio 打包应用程序][vs-package-command]

打包完成后，该包的位置将显示在“**输出**”窗口中。 请注意，当你在 Visual Studio 中部署或调试应用程序时，打包步骤自动发生。

### <a name="build-a-package-by-command-line"></a>通过命令行生成一个包
还可以使用 `msbuild.exe` 以编程方式打包你的应用程序。 这是 Visual Studio 实际运行的操作，因此输出是相同的。

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>测试包
可以使用 [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) 命令，通过 PowerShell 在本地验证包结构。
此命令将检查是否存在清单分析问题，并验证所有引用。 此命令只验证包中目录与文件结构的正确性。
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

如果应用程序已定义[应用程序参数](service-fabric-manage-multiple-environment-app-configuration.md)，则可以将参数传递到 [Test-ServiceFabricApplicationPackage](https://docs.microsoft.com/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) 中进行适当验证。

如果知道在其中部署应用程序的群集，则建议将参数传递到映像存储区连接字符串。 在这种情况下，还需要针对已在群集中运行的前一应用程序版本对包进行验证。 例如，验证可检测是否部署了版本相同但内容不同的包。  

应用程序经过正确打包并通过验证后，如果需要压缩，则需要基于文件的大小和数量进行评估。 

## <a name="compress-a-package"></a>压缩包
包较大或包含大量文件时，可压缩该包以提高部署速度。 压缩可以减少文件的数量和包大小。
[应用程序包](service-fabric-deploy-remove-applications.md#upload-the-application-package)可能比上传未压缩的包所需时间更长，但[注册](service-fabric-deploy-remove-applications.md#register-the-application-package)和[注销应用程序类型](service-fabric-deploy-remove-applications.md#unregister-an-application-type)时速度更快。

压缩包和未压缩包的部署机制相同。 如果为压缩包，则存储在群集映像存储等位置，并且在应用程序运行前在节点上解压缩。
压缩会将有效的 Service Fabric 包替换为已压缩版本。 文件夹必须允许写入操作。 对已压缩的包运行压缩将不会产生任何更改。 

可以通过使用 `CompressPackage` 开关运行 Powershell 命令 [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) 来压缩包。 可以通过使用 `UncompressPackage` 开关运行同一命令来解压缩包。

以下命令可压缩包，但不会将包复制到映像存储区。 通过使用不带 `SkipCopy` 标志的 [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) 将压缩的包复制到一个或多个 Service Fabric 群集中。 包中现在包括 `code`、`config` 和 `data` 包的压缩文件。 不会压缩应用程序清单和服务清单，因为许多内部操作（例如，包共享、应用程序类型名称和某些验证的版本提取）均需要使用它们。
压缩清单会使这些操作无效。

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

或者，也可以使用 [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) 一步压缩并复制包。
如果包较大，请提供足够的超时时间，以为包压缩和上传到群集预留时间。
```
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

在内部，Service Fabric 将计算应用程序包的校验和以进行验证。 使用压缩时，将对每个包的压缩版本计算校验和。
如果复制了应用程序包的未压缩版本，并且想对此包使用压缩，则必须更改应用程序清单版本以避免校验和不匹配。
同样，如果上传了压缩版本的包，则必须更新应用程序清单版本才能使用未压缩的包。

现在已将包进行了正确打包、验证和压缩（如需要），因此可以将其[部署](service-fabric-deploy-remove-applications.md)到一个或多个 Service Fabric 群集。

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

