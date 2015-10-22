<properties
   pageTitle="Service Fabric 服务清单资源"
   description="如何在服务清单中描述资源"
   services="service-fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="anuragg"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/26/2015"
   wacn.date=""/>

# 服务清单资源

## 概述

服务清单允许声明/更改服务要使用的资源而无需更改已编译的代码。Service Fabric 支持对服务的终结点资源进行配置。可以通过应用程序清单中的 SecurityGroup 控制对服务清单中指定资源的访问。资源的声明允许在部署时更改这些资源而不是要求服务引入新的配置机制。

## 终结点

在服务清单中定义了终结点资源时，Service Fabric 从保留的应用程序端口范围中分配端口。此外，服务还可以请求在资源中使用特定端口。在不同群集节点上运行的服务副本可以分配不同的端口号，而运行在同一节点上的同一服务的副本共享同一个端口。此类端口可供服务副本用于各种目的，例如复制，侦听客户端请求等。

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint" Protocol="http"/>
    <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
    <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
  </Endpoints>
</Resources>
```

请参阅[配置有状态可靠服务](/documentation/articles/service-fabric-reliable-services-configuration)以了解有关从配置包设置文件 (settings.xml) 引用终结点的更多信息。

## 示例
以下服务清单在 &lt;资源&gt; 元素中定义了 1 个 TCP 终结点资源和 2 个 HTTP 终结点资源。

HTTP 终结点由 Service-Fabric 自动建立 ACL。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="SP1" Version="V1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Description>Test Service</Description>
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="PersistType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="CP1" Version="V1">
    <EntryPoint>
      <ExeHost>
        <Program>CB\Code.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="CP1.Config0" Version="V1" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http"/>
      <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
      <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
 

<!---HONumber=74-->