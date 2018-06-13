---
title: 将现有可执行文件部署到 Azure Service Fabric | Microsoft 文档
description: 了解如何将现有应用程序打包为来宾可执行文件，以便部署到 Service Fabric 群集。
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/02/2017
ms.author: mfussell
ms.openlocfilehash: 99d34d59bb9d55ff074d454fe4544917c4e91110
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205970"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>打包现有可执行文件并将其部署到 Service Fabric
将现有可执行文件打包为[来宾可执行文件](service-fabric-guest-executables-introduction.md)时，可以选择是使用 Visual Studio 项目模板，还是[手动创建应用程序包](#manually)。 使用 Visual Studio 时，新的项目模板会创建应用程序包结构和清单文件。

> [!TIP]
> 将现有 Windows 可执行文件打包到服务中的最简单方法就是使用 Visual Studio 以及在 Linux 上使用 Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>使用 Visual Studio 打包和部署现有可执行文件
Visual Studio 提供 Service Fabric 服务模板将来宾可执行文件部署到 Service Fabric 群集。

1. 依次选择“**文件**” > “**新建项目**”，并创建一个 Service Fabric 应用程序。
2. 选择“**来宾可执行文件**”作为服务模板。
3. 单击“**浏览**”，选择包含可执行文件的文件夹，并填充剩余参数，从而创建服务。
   * *代码包行为*。 可以设置为将文件夹中的所有内容复制到 Visual Studio 项目中，这在可执行文件不发生变化时很有用。 如果预期可执行文件会更改，并且希望能够动态选择新版本，则可以改为选择文件夹的链接。 在 Visual Studio 中创建应用程序项目时，可以使用链接的文件夹。 这样一来，可以从项目内链接到源位置，以便能够在来宾可执行文件的源目标中对它进行更新。 在生成时，应用程序包中包括这些更新。
   * *Program* 指定为了启动服务应运行的可执行文件。
   * *Arguments* 指定应传递给可执行文件的参数。 它可以是带有实参的形参的列表。
   * *WorkingFolder* 指定要启动的进程的工作目录。 可以指定三个值：
     * `CodeBase` 指定将应用程序包中的 Code 目录（如上述文件结构中的 `Code` 目录所示）设为工作目录。
     * `CodePackage` 指定将应用程序包的根目录（如上述文件结构中的 `GuestService1Pkg` 目录所示）设为工作目录。
     * `Work` 指定文件会被置于名为 work 的子目录中。
4. 为服务命名，并单击“**确定**”。
5. 如果服务需要通信终结点，现在可以在 ServiceManifest.xml 文件中添加协议、端口和类型。 例如：`<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`。
6. 现在，可以通过在 Visual Studio 中调试解决方案，针对本地群集执行打包和发布操作。 完成后，可以将应用程序发布到远程群集，也可以将解决方案签入源控件。
7. 请阅读[检查正在运行的应用程序](#check-your-running-application)，了解如何查看在 Service Fabric Explorer 中运行的来宾可执行服务。

有关示例演练，请参阅[使用 Visual Studio 创建第一个来宾可执行应用程序](quickstart-guest-app.md)。

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>使用 Yeoman 在 Linux 上打包和部署现有可执行文件

用于在 Linux 上创建和部署来宾可执行文件的过程与部署 csharp 或 java 应用程序相同。

1. 在终端中，键入 `yo azuresfguest`。
2. 命名应用程序。
3. 命名服务，并提供详细信息，包括可执行文件的路径以及调用该服务所必须使用的参数。

Yeoman 创建应用程序包，其中包含相应的应用程序和清单文件，以及安装和卸载脚本。

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>手动打包和部署现有可执行文件
手动打包来宾可执行文件的常规步骤如下：

1. 创建包目录结构。
2. 添加应用程序的代码和配置文件。
3. 编辑服务清单文件。
4. 编辑应用程序清单文件。

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>创建包目录结构
可以先按上述部分“应用程序包文件结构”中的说明操作，创建目录结构。

### <a name="add-the-applications-code-and-configuration-files"></a>添加应用程序的代码和配置文件
创建了目录结构之后，可以在 code 和 config 目录下添加应用程序的代码和配置文件。 还可以在 code 或 config 目录下创建其他目录或子目录。

Service Fabric 对应用程序根目录下的内容执行了 `xcopy`，因此除创建 code 和 settings 这两个顶级目录以外没有其他任何预定义结构可以使用。 （如有需要可以选取其他名称。 有关详细信息，请参阅下一节内容。）

> [!NOTE]
> 请务必添加应用程序需要的所有文件和依赖项。 Service Fabric 会复制群集中所有节点上的应用程序包的内容，会在群集中部署应用程序的服务。 包应包含应用程序运行所需的全部代码。 请不要假定依赖项已安装。
>
>

### <a name="edit-the-service-manifest-file"></a>编辑服务清单文件
下一步是编辑服务清单文件以包含如下信息：

* 服务类型的名称。 这是 Service Fabric 用于标识服务的 ID。
* 用于启动应用程序的命令 (ExeHost)。
* 设置应用程序所需运行的任何脚本 (SetupEntrypoint)。

下面是一个 `ServiceManifest.xml` 文件的一个示例：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

下列各部分介绍了需要更新的文件不同部分。

#### <a name="update-servicetypes"></a>更新 ServiceTypes
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* 可以为 `ServiceTypeName` 选取所需的任何名称。 该值在 `ApplicationManifest.xml` 文件中用于标识服务。
* 指定 `UseImplicitHost="true"`。 此属性告知 Service Fabric 服务基于自包含的应用，因此 Service Fabric 只需要将其作为进程启动并监视其运行状况。

#### <a name="update-codepackage"></a>更新 CodePackage
CodePackage 元素指定服务代码的位置（和版本）。

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name` 元素用于在包含服务代码的应用程序包中指定目录的名称。 `CodePackage` 也有 `version` 属性。 这不仅可用于指定代码的版本，还可用于升级服务的代码，具体方法为在 Service Fabric 中使用应用程序生命周期管理基础结构。

#### <a name="optional-update-setupentrypoint"></a>可选：更新 SetupEntrypoint
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
SetupEntryPoint 元素用于指定在启动服务代码之前应执行的任何可执行文件或批处理文件。 此步骤是可选的，所以，如果无需初始化，也就无需执行这一步。 每次重新启动服务时，会执行 SetupEntryPoint。

由于只有一个 SetupEntryPoint，因此，如果应用程序的设置需要多个脚本，必须将设置脚本归入一个批处理文件中。 SetupEntryPoint 可以执行任意类型的文件：可执行文件、批处理文件和 PowerShell cmdlet。 有关详细信息，请参阅[配置 SetupEntryPoint](service-fabric-application-runas-security.md)。

在上面的示例中，SetupEntryPoint 会运行位于 code 目录的 `scripts` 子目录中的 `LaunchConfig.cmd` 批处理文件（假定 WorkingFolder 元素设置为 CodeBase）。

#### <a name="update-entrypoint"></a>更新 EntryPoint
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

服务清单文件中的 `EntryPoint` 元素用于指定如何启动该服务。 `ExeHost` 元素指定应用于启动该服务的可执行文件（和参数）。

* `Program` 指定应启动服务的可执行文件的名称。
* `Arguments` 指定应传递给可执行文件的参数。 它可以是带有实参的形参的列表。
* `WorkingFolder` 指定要启动的进程的工作目录。 可以指定三个值：
  * `CodeBase` 指定工作目录将设置为应用程序包中的 code 目录（前述文件结构中的 `Code` 目录）。
  * `CodePackage` 指定将应用程序包的根目录（如上述文件结构中的 `GuestService1Pkg` 所示）设为工作目录。
    * `Work` 指定文件会被置于名为 work 的子目录中。

WorkingFolder 用于设置正确的工作目录，以便应用程序或初始化脚本可以使用相对路径。

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>更新终结点并向命名服务注册以便通信
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
在上面的示例中，`Endpoint` 元素指定应用程序可以侦听的终结点。 在此示例中，Node.js 应用程序在端口 3000 上侦听 http。

此外，可以请求 Service Fabric 将此终结点发布到命名服务，以便其他服务可以发现指向此服务的终结点地址。 这样便能够在来宾可执行文件服务之间进行通信。
已发布的终结点地址格式为 `UriScheme://IPAddressOrFQDN:Port/PathSuffix`。 `UriScheme` 和 `PathSuffix` 是可选属性。 `IPAddressOrFQDN` 是此可执行文件所在节点的 IPAddress 或完全限定域名，并且自动计算。

在下面的示例中，在服务部署后，便会在 Service Fabric Explorer 中看到为服务实例发布的终结点，类似于 `http://10.1.4.92:3000/myapp/`。 如果是在本地计算机上，会看到 `http://localhost:3000/myapp/`。

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
可以将这些地址与[反向代理](service-fabric-reverseproxy.md)一起使用，即可在服务之间进行通信。

### <a name="edit-the-application-manifest-file"></a>编辑应用程序清单文件
配置 `Servicemanifest.xml` 文件之后，需要对 `ApplicationManifest.xml` 文件进行一些更改，以确保使用正确的服务类型和名称。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
在 `ServiceManifestImport` 元素中，可以指定一个或多个希望包含在应用中的服务。 `ServiceManifestName` 指定 `ServiceManifest.xml` 文件所在的目录名称，用来参考服务。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>设置日志记录
对于来宾可执行文件，最好能够查看控制台日志，以查明应用程序和配置脚本是否显示了任何错误。
可以使用 `ConsoleRedirection` 元素在 `ServiceManifest.xml` 文件中配置控制台重定向。

> [!WARNING]
> 永远不要在生产中部署的应用程序中使用控制台重定向策略，因为这可能会影响应用程序故障转移。 *仅*将其用于本地开发和调试目的。  
>
>

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

`ConsoleRedirection` 可用于将控制台输出（stdout 和 stderr）重定向到工作目录。 这样一来，可以确认在 Service Fabric 群集中设置或执行应用程序期间是否无任何错误。

`FileRetentionCount` 确定保存在工作目录中的文件的数量。 例如，值 5 表示前 5 个执行的日志文件存储在工作目录中。

`FileMaxSizeInKb` 指定日志文件的大小上限。

日志文件保存在服务的一个工作目录中。 若要确定文件所在位置，请使用 Service Fabric Explorer 确定运行服务的节点以及所使用的工作目录。 本文中后面部分介绍了此过程。

## <a name="deployment"></a>部署
最后一步是[部署应用程序](service-fabric-deploy-remove-applications.md)。 下面的 PowerShell 脚本展示了如何将应用程序部署到本地开发群集，然后启动新的 Service Fabric 服务。

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> 如果包较大或包含多个文件，请先[压缩包](service-fabric-package-apps.md#compress-a-package)，然后将其复制到映像存储区。 在[此处](service-fabric-deploy-remove-applications.md#upload-the-application-package)了解详细信息。
>

Service Fabric 服务可以采用各种“配置”进行部署。 例如，可以部署为单个或多个实例，也可以部署为在 Service Fabric 群集的每个节点上都有一个服务实例。

`New-ServiceFabricService` cmdlet 的 `InstanceCount` 参数用于指定应在 Service Fabric 群集中启动的服务实例的数量。 可以根据要部署的应用程序的类型来设置 `InstanceCount` 值。 最常见的两种方案是：

* `InstanceCount = "1"`。 在此用例中，群集上只部署一个服务实例。 Service Fabric 的计划程序确定会在哪一个节点上部署服务。
* `InstanceCount ="-1"`。 在此用例中，会在 Service Fabric 群集中的每个节点上部署一个服务实例。 结果是，群集中的每个节点都拥有一个（且只有一个）服务实例。

这是适用于前端应用程序（例如，REST 终结点）的实用配置，因为客户端应用程序需要“连接”群集中的任意节点，即可使用终结点。 此配置也适用于其他应用场景，例如，当 Service Fabric 群集的所有节点都连接负载均衡器时。 然后，可将客户端流量分布到群集中所有节点上运行的服务中。

## <a name="check-your-running-application"></a>检查正在运行的应用程序
在 Service Fabric Explorer 中，确定服务在其中运行的节点。 在此示例中，它在节点 1 上运行：

![运行服务的节点](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

如果导航到该节点并浏览到应用程序，则会看到基本节点信息（包括其在磁盘上的位置）。

![磁盘上的位置](./media/service-fabric-deploy-existing-app/locationondisk2.png)

如果使用服务器资源管理器转到目录，可以找到工作目录和服务的日志文件夹，如以下屏幕截图所示： 

![日志的位置](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>后续步骤
在本文中，我们了解了如何打包来宾可执行文件并将它部署到 Service Fabric。 请参阅下面几篇文章，了解相关信息和任务。

* [打包和部署来宾可执行文件的示例](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)，包括打包工具的预发行版本的链接
* [使用 REST 通过命名服务进行通信的两种来宾可执行文件（C# 和 nodejs）示例](https://github.com/Azure-Samples/service-fabric-containers)
* [部署多个来宾可执行文件](service-fabric-deploy-multiple-apps.md)
* [使用 Visual Studio 创建第一个 Service Fabric 应用程序](service-fabric-create-your-first-application-in-visual-studio.md)
