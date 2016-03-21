<properties
   pageTitle="Service Fabric 项目创建后续步骤 | Microsoft Azure"
   description="本文包含针对 Service Fabric 执行的一组核心开发任务的链接"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="12/06/2015"
   wacn.date=""/>

# Service Fabric 应用程序和后续步骤
已创建你的 Azure Service Fabric 应用程序。本文说明项目的构成以及有可能要执行的一些后续步骤。

## 你的应用程序
每个新应用程序都包含一个应用程序项目。根据选择的服务类型，可能有一个或两个附加的项目。

### 应用程序项目
应用程序项目包括：

- 对构成应用程序的服务的引用集。

- 两个发布配置文件（本地和云），可用于维护首选项以适应不同的环境 - 例如，与群集终结点相关的首选项，以及是否按默认执行升级部署。

- 两个应用程序参数文件（本地和云），可用于维护环境特定的应用程序配置，例如，要为服务创建的分区数目。

- 部署脚本，可用于从命令行部署应用程序，或者通过自动持续集成管道部署应用程序。

- 用于描述应用程序的应用程序清单。

### Reliable Services
当你添加新的可靠服务时，Visual Studio 会将一个服务项目添加到你的解决方案。该服务项目中包含从 `StatelessService` 或 `StatefulService`（具体取决于所选的类型）扩展的类。

### Reliable Actors
当你添加新的可靠执行组件时，Visual Studio 会将两个项目添加到你的解决方案：执行组件项目和接口项目。

执行组件项目定义执行组件类型（适用于有状态执行组件）及其状态。接口项目提供其他服务可用来调用执行组件的接口。

请注意，执行组件项目不包含任何默认启动行为，因为执行组件必须由其他服务激活。请考虑添加一个可靠服务或 ASP.NET 项目来创建执行组件并与其交互。

### ASP.NET 5
在 Service Fabric 应用程序中使用的 ASP.NET 5 模板与针对独立创建的 ASP.NET 5 项目提供的模板几乎完全相同。唯一的差别是：

- 项目包含一个 **PackageRoot** 文件夹，用于存储 ServiceManifest 文件及数据和配置包。

- 项目引用另一个 NuGet 包 (Microsoft.ServiceFabric.AspNet.Hosting)，该包充当 .NET 执行环境 (DNX) 与 Service Fabric 之间的桥梁。

## 后续步骤
### 将 Web 前端添加到应用程序
Service Fabric 提供与 ASP.NET 5 的集成，用于构建应用程序的基于 Web 的入口点。若要了解如何基于 ASP.NET Web API 创建 REST 接口，请参阅[将 Web 前端添加到应用程序][add-web-frontend]。

### 创建 Azure 群集
Service Fabric SDK 提供一个用于开发和测试的本地群集。若要在 Azure 中创建群集，请参阅[从 Azure 门户设置 Service Fabric 群集][create-cluster-in-portal]。

### 尝试使用合作群集免费部署到 Azure

如果你要尝试在 Azure 中部署和管理应用程序且不设置自己的群集，可以使用免费的[合作群集服务](http://tryazureservicefabric.eastus.cloudapp.azure.com/)。

### 将应用程序发布到 Azure
可以直接从 Visual Studio 将应用程序发布到 Azure 群集。若要了解操作方法，请参阅[将应用程序发布到 Azure][publish-app-to-azure]。

### 使用 Service Fabric 资源管理器可视化群集
Service Fabric 资源管理器提供一种用于可视化群集（包括已部署的应用程序和物理布局）的简易方法。有关详细信息，请参阅[使用 Service Fabric 资源管理器可视化群集][visualize-with-sfx]。

### 对服务进行版本控制和升级
Service Fabric 支持单独对应用程序中的独立服务进行版本控制和升级。若要了解详细信息，请参阅[对服务进行版本控制和升级][app-upgrade-tutorial]。

### 配置与 Visual Studio Team Services 的持续集成
若要了解如何为 Service Fabric 应用程序设置持续集成过程，请参阅[配置与 Visual Studio Team Services 的持续集成][ci-with-vso]。



<!-- Links -->
[add-web-frontend]: /documentation/articles/service-fabric-add-a-web-frontend
[create-cluster-in-portal]: /documentation/articles/service-fabric-cluster-creation-via-portal
[publish-app-to-azure]: /documentation/articles/service-fabric-publish-app-remote-cluster
[visualize-with-sfx]: /documentation/articles/service-fabric-visualizing-your-cluster
[ci-with-vso]: /documentation/articles/service-fabric-set-up-continuous-integration
[reliable-services-webapi]: /documentation/articles/service-fabric-reliable-services-communication-webapi
[app-upgrade-tutorial]: /documentation/articles/service-fabric-application-upgrade-tutorial

<!---HONumber=Mooncake_0314_2016-->