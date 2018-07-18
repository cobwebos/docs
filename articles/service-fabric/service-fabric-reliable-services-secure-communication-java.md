---
title: 在 Azure Service Fabric 中通过 Java 保护服务远程处理通信 | Microsoft Docs
description: 了解如何保护 Azure Service Fabric 群集中运行的 Java 可靠服务的基于服务远程处理的通信。
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: cbefb3ede6d0d1fe21065b49c84db9f4db5dd39c
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37020807"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>保护 Java 服务的服务远程处理通信
> [!div class="op_single_selector"]
> * [Windows 上的 C#](service-fabric-reliable-services-secure-communication.md)
> * [Linux 上的 Java](service-fabric-reliable-services-secure-communication-java.md)
>
>

安全是通信最为重视的要素之一。 Reliable Services 应用程序框架提供了一些预先构建的通信堆栈和工具，可用来提高安全性。 本文介绍如何在 Java 服务中使用服务远程处理时提高安全性。 它基于现有的[示例](service-fabric-reliable-services-communication-remoting-java.md)构建，该示例解释了如何为使用 Java 编写的可靠服务设置远程处理。 

若要在 Java 服务中使用服务远程处理时帮助保护服务，请遵循以下步骤：

1. 创建接口 `HelloWorldStateless`，用于定义可供服务的远程过程调用使用的方法。 服务将使用 `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` 包中声明的 `FabricTransportServiceRemotingListener`。 这是可以提供远程处理功能的 `CommunicationListener` 实现。

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. 添加侦听器设置和安全凭据。

    确保要用来帮助保护服务通信的证书安装在群集中的所有节点上。 对于在 Linux 上运行的服务，证书必须以 PEM 格式的文件提供，要么是包含证书和私钥的 `.pem` 文件，要么是包含证书的 `.crt` 文件和包含私钥的 `.key` 文件。 若要了解详细信息，请参阅 [Linux 节点上的 X.509 证书的位置和格式](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes)。
    
    有两种方式可用于提供侦听器设置和安全凭据：

   1. 使用[配置包](service-fabric-application-and-service-manifests.md)提供：

       在 settings.xml 文件中添加名为 `TransportSettings` 的节。

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       在这种情况下，`createServiceInstanceListeners` 方法如下所示：

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        如果将在 settings.xml 中添加 `TransportSettings` 节而不添加任何前缀，则 `FabricTransportListenerSettings` 将按默认加载此节中的所有设置。

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        在这种情况下，`CreateServiceInstanceListeners` 方法如下所示：

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. 在安全服务上使用远程堆栈（而不是使用 `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` 类）调用方法来创建服务代理时，请使用 `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`。

    如果客户端代码正在作为服务的一部分运行，则可以从 settings.xml 文件中加载 `FabricTransportSettings`。 创建与服务代码类似的 TransportSettings 节，如上所示。 对客户端代码进行以下更改。

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
