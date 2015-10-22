<properties
   pageTitle="Node.js 和 Reliable Actors | Microsoft Azure"
   description="有关如何构建使用 Reliable Actors 并在 Azure Service Fabric 平台顶部运行的 node.js express 应用程序的演练。"
   services="service-fabric"
   documentationCenter="nodejs"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/17/2015"
   wacn.date=""/>


# Node.js 和 Reliable Actors：成功的组合
本文是对如何使用 node.js 和 Reliable Actors 构建应用程序的概述。最终的解决方案是主要用于提供 app (Web/Rest API) 的前端的 javascript 代码和适用于更复杂的计算的 C# 的混合方案。通过利用 Service Fabric 编程模型，应用程序在初始状态下可缩放且可靠。
过程基于以下步骤：

1. 使用适用于 Visual Studio 的 Service Fabric 工具创建新的 Service Fabric 无状态或有状态 Actor 项目
2. 使用诸如[适用于 Visual Studio 的 node.js 工具](https://github.com/Microsoft/nodejstools/releases/tag/v1.1.RC)等创建 node.js 项目 
3. 将 node.js 项目（例如基本 Express 4 应用）添加到 Service Fabric 解决方案中。你可以只使用解决方案/添加现有项目来包括 node.js 项目。 
4. 打包 node.js 应用，以便可以使用适用于 Service Fabric 的 VS 工具部署它

## 创建 node.js 项目
创建 node.js 项目并添加依赖项后，你需要更改项目的目录结构，以便它遵循适用于 Visual Studio 的 Service Fabric 工具所需的结构，从而能够打包和部署任何其他 Service Fabric 服务等应用。目标是只对目录结构更改一次，以便你可以从能够在 Visual Studio 中使用部署过程中获益，并因此部署你的 node.js 应用以及解决方案中的其他服务。
需要对目录结构进行的更改是：

1. 创建 PackageRoot 目录
2. 在 PackageRoot 下创建 Code 目录
3. 移动 Code 目录下的所有文件和目录

完成后，Visual Studio 中的项目结构应如下所示：

![][8]

正如你所见，所有的 node.js 代码都位于 PackageRoot/Code 目录下。Service Fabric 不会对其中将要部署应用程序的节点上安装的应用程序/库作出任何假设，因此需要包括所有依赖项。如果是 node.js，你需要包括 node.exe，这样 Service Fabric 就可以运行代码（你可以在 Program Files\\nodejs 目录下找到 node.exe）。

![][3]

## 添加 servicemanifest.xml 元数据文件
若要部署 node.js 应用程序，我们需要添加指定某些属性所需的元数据文件，这些属性将由 Service Fabric 使用，以便确定如何部署和启动应用程序。

这是关于 servicemanifest.xml 应类似的示例，有关需要更新的重要元素的详细信息，请参阅[本文](service-fabric-deploy-existing-app)，但通常你需要更新以下内容：

* 名称（ServiceManifest 元素）
* ServiceTypeName（StatelessServiceType 元素）
* 指定应该用于启动应用的 js 文件的参数（ExeHost 元素）。


```xml

<?xml version="1.0" encoding="utf-8"?>

<ServiceManifest Name="NodejsFrontendPkg"
                 Version="1.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="NodejsFrontendType" UseImplicitHost="true" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>node.exe</Program>
        <Arguments>server.js</Arguments>
        <WorkingFolder>CodeBase</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0.0" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

> [AZURE.NOTE]请注意，servicemanifest.xml 的其中一个选项可能不适用于 node.js。在某些情况下，`ConsoleRedirection` 不适用，条件是 node.js 模块（例如：Express）假定 stdout 和 stderr 的 fd 为 1 和 2。

`servicemanifest.xml` 文件添加到 node.js 项目中之后，项目结构应如下所示：

![][4]

## 添加 Service Fabric 二进制文件
下一步是添加用于连接 Service Fabric 群集中运行的 Actor 的 Service Fabric 二进制文件。如你在 Edge.js 示例中所见，有一些对程序集的引用。若要使这些程序集可用于 edge.js，需要与 node.js 代码一起复制它们。
最简单的方法是从现有项目中复制二进制文件，需要包含在代码目录（将由 edge.js 使用）中的文件如下所示：

```
Microsoft.ServiceFabric.Actors.dll
Microsoft.ServiceFabric.Collections.dll
Microsoft.ServiceFabric.Data.dll
Microsoft.ServiceFabric.Data.Log.dll
Microsoft.ServiceFabric.ReplicatedStore.dll
Microsoft.ServiceFabric.Replicator.dll
Microsoft.ServiceFabric.Services.dll
ServiceFabricServiceModel.dll
System.Fabric.Common.Internal.dll
System.Fabric.Common.Internal.Strings.resources.dll
System.Fabric.dll
```

将二进制文件添加到项目后，项目结构应如下所示：

![][5]

## 将引用添加到 applicationmanifest 文件中的 node.js 项目
下一步是将 node.js 服务添加到应用程序清单中，以便可以使用适用于 Service Fabric 的Visual Studio 工具部署它。这是必要步骤，因为适用于 Visual Studio 的 Service Fabric 工具和 node.js 项目没有集成，所以需要手动添加。

在 `applicationmanifest.xml` 文件中，你需要添加以下元素：

* `ServiceManifestImport`
* `Service`

> [AZURE.NOTE]请确保使用与在 `servicemanifest.xml` 中用于指定 `ServiceName` 和 `ServiceTypeName` 相同的名称，`applicationmanifest.xml` 文件应与以下内容类似：

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeServiceFabricSampleApplication" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeServiceFabricSamplePkg" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="VisualObjectsNodejsWebServicePkg" ServiceManifestVersion="1.0.0.0" />
       </ServiceManifestImport>
   <DefaultServices>
      <Service Name="NodeServiceFabricSampleActorService">
         <StatelessService ServiceTypeName="NodeServiceFabricSampleActorServiceType">
            <UniformInt64Partition PartitionCount="9" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatelessService>
      </Service>
     <Service Name="NodejsFrontendPkg">
       <StatelessService ServiceTypeName="NodejsFrontendType">
         <SingletonPartition />
       </StatelessService>
     </Service>
   </DefaultServices>
</ApplicationManifest>

```

## 在 Node.js 应用中使用 Reliable Actors
现在已设置项目结构，我们可以关注能够用于将 node.js 应用程序连接到群集中的 Reliable Actors 的代码，就像可以从.NET 应用程序中操作一样。我们想要启用的方案是构建 node.js 应用程序，该应用程序充当客户端应用程序的前端/网关，并公开基于 Web 的应用程序或一组 rest API 客户端应用程序可以使用。
Node.js 提供应用的前端，而 Reliable Actors 提供可缩放和可靠的应用的“应用程序服务器”层。
在 Service Fabric 中，可以使用 [ActorProxy 类](service-fabric-reliable-actors-introduction#actor-communication)调用 Reliable Actors。幸运的是，有一个可用于调入.NET 代码的强大 node.js 模块：[Edge.js](https://github.com/tjanczuk/edge)。可以使用 github 存储库中的说明了解如何在你的计算机上安装边缘。

 
![][2]

使用 NPM 或 NPM UI 在 Visual Studio 中安装边缘后，你需要将新的已安装模块移动到 Code 子目录下的 node\_modules 目录中（我们更改了 node.js 项目结构并将所有代码移动到 PackageRoot/Code 目录下）。
你可以在 Github 上的 edge.js 存储库中找到有关如何使用边缘调用.NET 代码的很好的示例，以下是当你需要通过 ActorProxy 类调用 Reliable Actors 时代码类似的一个简单示例。

```javascript

var getActorStatus = edge.func(function () {

/*

    #r "Microsoft.ServiceFabric.Actors.dll"
    #r "System.Globalization.dll"
    #r "System.dll"
    #r  "visualobjects.interfaces.dll"
    
    using Microsoft.ServiceFabric.Actors;
    using System.Globalization;
    using VisualObjects.Interfaces;
    using System.Threading.Tasks;
    using System; 


public class Startup
    {
         public async Task<object> Invoke(dynamic input)
        {

            var objectId = (string) input.actorId;
            var serviceUri = (string) input.serviceUri;
            var actId = new ActorId(objectId);
            var serId = new Uri(serviceUri);
            var actorProxy = ActorProxy.Create<IVisualObjectActor>(actId, serId);
            var buffer = await actorProxy.GetStateAsJsonAsync();
            if (!string.IsNullOrEmpty(buffer))
                return buffer;
                else 
                return "null";
         }
  }
 
 */ 

});

```

> [AZURE.NOTE]ActorProxy 使用 Actor 接口了解它应连接到的 Actor。为了使 node.js 过程中运行的托管代码能够实例化该类，需要在诸如其他 Service Fabric dll 的代码目录中复制 Actor 接口程序集。
> 注意：在接口中更改方法/参数时必须随时复制（或在 VS 中设置后期生成操作）dll。

![][6]

## 部署
此时可以使用适用于 Visual Studio 的 Service Fabric 工具部署项目。过程的最后一步是引用 Service Fabric 应用程序项目中的项目，以便它可以包括在应用程序包中并在群集中部署。

![][9]
 
你可以通过使用诸如解决方案上下文菜单等部署应用程序（将包括 node.js 应用程序）。

![][10]

## 后续步骤
* 了解有关 [Reliable Actors](service-fabric-reliable-actors-introduction) 的详细信息
* 了解有关 Service Fabric [应用程序生命周期](service-fabric-application-lifecycle)的详细信息
* 了解有关升级 [Service Fabric 应用程序](service-fabric-application-upgrade)的详细信息 

<!-- images -->
[1]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure.PNG
[2]: ./media/service-fabric-node-and-reliable-actors-app/edge-js.PNG
[3]: ./media/service-fabric-node-and-reliable-actors-app/node-exe.PNG
[4]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-manifest.PNG
[5]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-dlls.PNG
[6]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-interface-dll.PNG
[7]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-config.PNG
[8]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure1.PNG
[9]: ./media/service-fabric-node-and-reliable-actors-app/application-project-reference.PNG
[10]: ./media/service-fabric-node-and-reliable-actors-app/solution-deploy.PNG

<!---HONumber=74-->