---
title: "Azure Service Fabric 应用程序模型 | Microsoft Docs"
description: "如何在 Service Fabric 中建模和描述应用程序。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: fa7b48225698a2e210d61656ffff0225227e2ab5
ms.contentlocale: zh-cn
ms.lasthandoff: 06/30/2017


---
# <a name="model-an-application-in-service-fabric"></a>在 Service Fabric 中对应用程序建模
本文概述 Azure Service Fabric 应用程序模型以及如何通过清单文件定义应用程序和服务。

## <a name="understand-the-application-model"></a>了解应用程序模型
应用程序是由执行一个或多个特定功能的成分服务组成的集合。 服务执行完整且独立的功能，可以独立于其他服务启动和运行。  服务由代码、配置和数据组成。 对于每个服务，代码由可执行二进制文件组成，配置由可在运行时加载的服务设置组成，数据则由可供该服务使用的任意静态数据组成。 可对此层次应用程序模型中的每个组件进行独立的版本控制和升级。

![Service Fabric 应用程序模型][appmodel-diagram]

应用程序类型是一个应用程序分类，包含大量服务类型。 服务类型是一个服务分类。 该分类可以具有不同的设置和配置，但核心功能保持相同。 服务实例是相同服务类型的不同服务配置变体。  

通过 XML 文件（应用程序清单和服务清单）描述应用程序和服务的类（或“类型”）。  清单即模板，可对照这样的模板从群集的映像存储区中使应用程序实例化。 ServiceManifest.xml 和 ApplicationManifest.xml 文件的架构定义随 Service Fabric SDK 和工具一起安装到 *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*。

即使在由同一 Service Fabric 节点托管时，不同应用程序实例的代码也将作为单独的进程运行。 此外，可以独立管理每个应用程序实例的生命周期（如升级）。 下图显示了应用程序类型如何由服务类型组成，而服务类型又如何由代码、配置和数据包组成。 为了简化示意图，这里只显示了 `ServiceType4` 的代码/配置/数据包，不过每个服务类型包括其中的部分或全部包类型。

![Service Fabric 应用程序类型和服务类型][cluster-imagestore-apptypes]

使用了两种不同的清单文件来描述应用程序和服务，即服务清单和应用程序清单。 以下部分详细介绍了清单。

群集中可以有一个或多个服务类型实例处于活动状态。 例如，有状态服务实例或副本通过复制位于群集中不同节点上的副本之间的状态实现高可靠性。 复制本质上是提供冗余，使服务即使在群集中的一个节点失败时也可用。 [分区服务](service-fabric-concepts-partitioning.md)进一步跨群集中的节点划分其状态（和该状态的访问模式）。

下图显示应用程序和服务实例、分区与副本之间的关系。

![服务中的分区和副本][cluster-application-instances]

> [!TIP]
> 可以使用 http://&lt;yourclusteraddress&gt;:19080/Explorer 上提供的 Service Fabric Explorer 工具查看群集中应用程序的布局。 有关详细信息，请参阅[使用 Service Fabric Explorer 可视化群集](service-fabric-visualizing-your-cluster.md)。
> 
> 

## <a name="describe-a-service"></a>描述服务
服务清单以声明方式定义服务类型和版本。 它指定服务元数据，例如服务类型、运行状况属性、负载均衡度量值、服务二进制文件和配置文件。  换言之，它描述了组成一个服务包以支持一个或多个服务类型的代码、配置和数据包。 下面是服务清单的简单示例：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
    <EnvironmentVariables>
      <EnvironmentVariable Name="MyEnvVariable" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentVariables>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
```

**Version** 特性是未结构化的字符串，并且不由系统进行分析。 版本特性用于对每个组件进行版本控制，以进行升级。

**ServiceTypes** 声明此清单中的 **CodePackages** 支持哪些服务类型。 当一种服务针对这些服务类型之一进行实例化时，可激活此清单中声明的所有代码包，方法是运行这些代码包的入口点。 生成的进程应在运行时注册所支持的服务类型。 在清单级别而不是代码包级别声明服务类型。 因此，当存在多个代码包时，每当系统查找任何一种声明的服务类型时，它们都将被激活。

**SetupEntryPoint** 是特权入口点，以与 Service Fabric（通常是 *LocalSystem* 帐户）相同的凭据先于任何其他入口点运行。 **EntryPoint** 指定的可执行文件通常是长时间运行的服务主机。 提供单独的设置入口点可避免长时间使用高特权运行服务主机。 由 **EntryPoint** 指定的可执行文件在 **SetupEntryPoint** 成功退出后运行。 如果进程总是终止或出现故障，则监视并重启所产生的过程（再次从“SetupEntryPoint”开始）。  

“SetupEntryPoint”的典型使用场景是在服务启动之前运行可执行文件，或使用提升的权限来执行操作时。 例如：

* 设置和初始化服务可执行文件所需的环境变量。 这并不限于通过 Service Fabric 编程模型编写的可执行文件。 例如，npm.exe 需要配置一些环境变量来部署 node.js 应用程序。
* 通过安装安全证书设置访问控制。

有关如何配置 **SetupEntryPoint** 的详细信息，请参阅[配置服务设置入口点的策略](service-fabric-application-runas-security.md)

**EnvironmentVariables** 提供为此代码包设置的环境变量列表。 环境变量可以在 `ApplicationManifest.xml` 中重写，以便为不同的服务实例提供不同的值。 

**DataPackage** 声明一个由 **Name** 特性命名的文件夹，该文件夹中包含进程将在运行时使用的静态数据。

**ConfigPackage** 声明一个由 **Name** 特性命名的文件夹，该文件夹中包含 *Settings.xml* 文件。 此设置文件包含用户定义的键值对设置部分，进程可在运行时读回这些设置。 升级期间，如果仅更改了 **ConfigPackage** **版本**，则不重启正在运行的进程。 相反，回调会向进程通知配置设置已更改，以便可以重新动态加载这些设置。 下面是 *Settings.xml* 文件的一个示例：

```xml
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

> [!NOTE]
> 服务清单可以包含多个代码、配置和数据包。 可对它们进行独立的版本控制。
> 
> 

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application"></a>描述应用程序
应用程序清单以声明方式描述应用程序类型和版本。 它指定服务组合元数据（如稳定名称、分区方案、实例计数/复制因子、安全/隔离策略、布置约束、配置替代和成分服务类型）。 此外还描述了将在其中放置应用程序的负载均衡域。

因此，应用程序清单在应用程序级别描述元素，并引用了一个或多个服务清单，以组成应用程序类型。 下面是应用程序清单的简单示例：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
    <ConfigOverrides/>
    <EnvironmentOverrides CodePackageRef="MyCode"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
```

类似于服务清单，**Version** 特性是未结构化的字符串，并且不由系统进行分析。 版本特性也用于对每个组件进行版本控制，以进行升级。

**ServiceManifestImport** 包含对组成此应用程序类型的服务清单的引用。 导入的服务清单将确定此应用程序类型中哪些服务类型有效。 在 ServiceManifestImport 中，可以重写 Settings.xml 中的配置值和 ServiceManifest.xml 文件中的环境变量。 


**DefaultServices** 声明每当一个应用程序依据此应用程序类型进行实例化时自动创建的服务实例。 默认服务只是提供便利，创建后，它们的行为在每个方面都与常规服务类似。 它们与应用程序实例中的任何其他服务一起升级，并且也可以将它们删除。

> [!NOTE]
> 应用程序清单可以包含多个服务清单导入和默认服务。 可对每个服务清单导入进行独立的版本控制。
> 
> 

若要了解如何维护不同的应用程序和用于单个环境的服务参数，请参阅[管理多个环境的应用程序参数](service-fabric-manage-multiple-environment-app-configuration.md)。

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>后续步骤
[打包应用程序](service-fabric-package-apps.md)并准备好进行部署。

[部署和删除应用程序][10]介绍如何使用 PowerShell 来管理应用程序实例。

[管理多个环境的应用程序参数][11]介绍如何配置不同应用程序实例的参数和环境变量。

[配置应用程序的安全策略][12]介绍如何在安全策略下运行服务，从而对访问进行限制。

[应用程序托管模型][13]说明已部署的服务和服务主机进程二者的副本（或实例）之间的关系。

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
[13]: service-fabric-hosting-model.md

