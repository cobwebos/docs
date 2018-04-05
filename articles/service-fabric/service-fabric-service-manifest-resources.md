---
title: 指定 Service Fabric 服务终结点 | Microsoft 文档
description: 如何在服务清单中描述终结点资源，包括如何设置 HTTPS 终结点
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: a028471871754c2b9c3981ec13f5788643675a77
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="specify-resources-in-a-service-manifest"></a>在服务清单中指定资源
## <a name="overview"></a>概述
服务清单允许声明/更改服务要使用的资源而无需更改已编译的代码。 Azure Service Fabric 支持对服务的终结点资源进行配置。 可以通过应用程序清单中的 SecurityGroup 控制对服务清单中指定资源的访问。 资源的声明允许在部署时更改这些资源，这意味着服务不需要引入新的配置机制。 ServiceManifest.xml 文件的架构定义随 Service Fabric SDK 和工具一起安装到 *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*。

## <a name="endpoints"></a>终结点
在服务清单中定义了终结点资源时，如果未显式指定端口，则 Service Fabric 从保留的应用程序端口范围中分配端口。 例如，可以查看本段落后面提供的清单代码段中指定的终结点 *ServiceEndpoint1*。 此外，服务还可以请求在资源中使用特定端口。 在不同群集节点上运行的服务副本可以分配不同的端口号，而运行在同一节点上的服务副本共享同一个端口。 之后服务副本可根据需要将这些端口用于复制和侦听客户端请求。

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

如果在单个服务包中有多个代码包，则还需要在“终结点”部分中引用代码包。  例如，如果 **ServiceEndpoint2a** 和 **ServiceEndpoint2b** 是同一个服务包中引用不同代码包的终结点，则对应于每个终结点的代码包按如下所示说明：

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

请参阅[配置有状态 Reliable Services](service-fabric-reliable-services-configuration.md)以了解有关从配置包设置文件 (settings.xml) 引用终结点的更多信息。

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>示例：为服务指定 HTTP 终结点
以下服务清单在 &lt;Resources&gt; 元素中定义了 1 个 TCP 终结点资源和 2 个 HTTP 终结点资源。

HTTP 终结点由 Service Fabric 自动建立 ACL。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>示例：指定用于服务的 HTTPS 终结点
HTTPS 协议提供服务器身份验证，用于对客户端-服务器通信进行加密。 若要在 Service Fabric 服务上启用 HTTPS，请在服务清单的“*资源 -> 终结点 -> 终结点*”部分中指定该协议，如前面针对终结点 *ServiceEndpoint3* 的操作所示。

> [!NOTE]
> 在应用程序升级期间不能更改服务的协议。 如果在升级期间进行了更改，那将是一项重大的更改。
> 
> 

下面是需要为 HTTPS 设置的一个示例 ApplicationManifest。 必须提供证书的指纹。 EndpointRef 是对 ServiceManifest 中 EndpointResource 的引用，为其设置 HTTPS 协议。 可以添加多个 EndpointCertificate。  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

对于 Linux 群集，**MY** 存储默认为文件夹 **/var/lib/sfcerts**。


## <a name="overriding-endpoints-in-servicemanifestxml"></a>重写 ServiceManifest.xml 中的终结点

在 ApplicationManifest 中，添加一个 ResourceOverrides 部分，作为 ConfigOverrides 部分的同级。 在本部分中，可以为服务清单中指定的资源部分中的终结点部分指定替代。 运行时 5.7.217/SDK 2.7.217 及更高版本支持替代终结点。

若要使用 ApplicationParameter 重写 ServiceManifest 中的终结点，请更改 ApplicationManifest，如下所示：

在 ServiceManifestImport 部分添加一个新部分“ResourceOverrides”

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

在参数中添加以下内容：

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

部署应用程序时，现可传入这些值作为 ApplicationParameter，例如：

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

注意：如果针对 ApplicationParameter 提供的值为空，将返回到 ServiceManifest 中为对应的 EndPointName 提供的默认值。

例如：

如果在你指定的 ServiceManifest 中

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

并且应用程序参数的 Port1 和 Protocol1 值为 null 或为空。 仍由 ServiceFabric 决定端口。 而协议将 TCP。

假设指定了错误值。 例如，对于“端口”，你指定了字符串值“Foo”而不是 int。新的 ServiceFabricApplication 命令将失败并出现错误：“ResourceOverrides”部分中的名称“ServiceEndpoint1”属性“Port1”的替代参数无效。 指定的值为“Foo”，而要求的值为“int”。
