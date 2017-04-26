---
title: "更改 Azure 微服务中的 FabricTransport 设置 | Microsoft Docs"
description: "了解如何配置 Azure Service Fabric 执行组件通信设置。"
services: Service-Fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: 
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: suchia
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 4cbca6e496135a312bf4704dd0989f45dcccfc00
ms.lasthandoff: 04/14/2017


---
# <a name="configure-fabrictransport-settings-for-reliable-actors"></a>配置 Reliable Actors 的 FabricTransport 设置

以下为用户可以配置的设置：

- C#： [FabricTansportSettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.fabrictransport.common.fabrictransportsettings)
- Java：[FabricTransportRemotingSettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.fabrictransport._fabric_transport_remoting_settings)

可以通过以下方式修改 FabricTransport 的默认配置。

## <a name="assembly-attribute"></a>程序集属性

需要在执行组件客户端和执行组件服务程序集上应用 [FabricTransportActorRemotingProvider](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute?redirectedfrom=MSDN#microsoft_servicefabric_actors_remoting_fabrictransport_fabrictransportactorremotingproviderattribute) 属性。

以下示例演示如何更改 FabricTransport OperationTimeout 设置的默认值：

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
   ```

以下示例演示如何更改 FabricTransport MaxMessageSize 和 OperationTimeoutInSeconds 的默认值：

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
   ```

## <a name="config-package"></a>配置包

可以使用[配置包](service-fabric-application-model.md)修改默认配置。

### <a name="configure-fabrictransport-settings-for-the-actor-service"></a>配置执行组件服务的 FabricTransport 设置

在 settings.xml 文件中添加 TransportSettings 节。

默认情况下，执行组件代码寻找“&lt;ActorName&gt;TransportSettings”形式的 SectionName。 如果未找到，则会查找“TransportSettings”形式的 SectionName。

  ```xml
  <Section Name="MyActorServiceTransportSettings">
       <Parameter Name="MaxMessageSize" Value="10000000" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
       <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
       <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
       <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
       <Parameter Name="CertificateStoreName" Value="My" />
       <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
       <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
   </Section>
  ```

### <a name="configure-fabrictransport-settings-for-the-actor-client-assembly"></a>配置执行组件客户端程序集的 FabricTransport 设置

如果客户端不是作为服务一部分运行，则可在客户端 .exe 文件所在的同一位置创建“&lt;Client Exe Name&gt;.settings.xml”文件。 然后在该文件中添加 TransportSettings 节。 SectionName 应为“TransportSettings”。

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <Section Name="TransportSettings">
      <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
      <Parameter Name="CertificateFindValue" Value="78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
      <Parameter Name="CertificateStoreName" Value="My" />
      <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
    </Section>
  </Settings>
   ```

