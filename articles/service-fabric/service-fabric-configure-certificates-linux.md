---
title: 在 Linux 上配置 Azure Service Fabric 应用程序的证书 | Microsoft Docs
description: 在 Linux 群集上使用 Service Fabric 运行时配置应用的证书
services: service-fabric
documentationcenter: NA
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: v-jamebr
ms.openlocfilehash: 2d6d387ed12e7261d09669686c0710786a4302dd
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025108"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Linux 群集上的证书和安全性

本文提供有关在 Linux 群集上配置 X.509 证书的信息。

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Linux 节点上的 X.509 证书的位置和格式

Service Fabric 通常要求在 Linux 群集节点上的 */var/lib/sfcerts* 目录中提供 X.509 证书。 这些证书包括群集证书、客户端证书，等等。在某些情况下，可为证书指定其他位置，而不一定要指定 *var/lib/sfcerts* 文件夹。 例如，在使用 Service Fabric Java SDK 生成的 Reliable Services 中，可以通过配置包 (Settings.xml) 为某些特定于应用程序的证书指定不同的位置。 有关详细信息，请参阅[配置包 (Settings.xml) 中引用的证书](#certificates-referenced-in-the-configuration-package-settingsxml)。

对于 Linux 群集，Service Fabric 要求以包含证书和私钥的 .pem 文件形式提供证书，或者以包含证书的 .crt 文件和包含私钥的 .key 文件形式提供证书。 所有文件应采用 PEM 格式。 

如果使用[资源管理器模板](./service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template)或 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/?view=latest#service_fabric) 命令从 Azure Key Vault 安装证书，证书将以正确的格式安装在每个节点上的 */var/lib/sfcerts* 目录中。 如果通过其他方法安装证书，必须确保在群集节点上正确安装证书。

## <a name="certificates-referenced-in-the-application-manifest"></a>应用程序清单中引用的证书

应用程序清单中指定的证书（例如，通过 [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) 或 [**EndpointCertificate**](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element) 元素指定）必须位于 */var/lib/sfcerts* 目录中。 用于在应用程序清单中指定证书的元素不采用路径属性，因此证书必须位于默认目录中。 这些元素采用可选的 **X509StoreName** 属性。 默认值为“My”，指向 Linux 节点上的 */var/lib/sfcerts* 目录。 其他任何值未在 Linux 群集上定义。 对于 Linux 群集上运行的应用，我们建议省略 **X509StoreName** 属性。 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>配置包 (Settings.xml) 中引用的证书

对于某些服务，可在 [ConfigPackage](./service-fabric-application-and-service-manifests.md)（默认为 Settings.xml）中配置 X.509 证书。 例如，在声明用于保护通过 Service Fabric .NET Core 或 Java SDK 生成的 Reliable Services 服务的 RPC 通道时，便可以这样做。 可在配置包中通过两种方式引用证书。 支持的方式根据使用的是 .NET Core 还是 Java SDK 而异。

### <a name="using-x509-securitycredentialstype"></a>使用 X509 SecurityCredentialsType

使用 .NET 或 Java SDK，可为 **SecurityCredentialsType** 指定 **X509**。 此值对应于 `SecurityCredentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric._security_credentials)) 类型的 `X509Credentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric._x509_credentials))。

**X509** 引用在证书存储中查找证书。 以下 XML 显示用于指定证书位置的参数：

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

对于 Linux 上运行的服务，**LocalMachine**/**My** 指向证书的默认位置，即 */var/lib/sfcerts* 目录。 对于 Linux，**CertificateStoreLocation** 和 **CertificateStoreName** 的其他任何组合均未定义。 

请始终为 **CertificateStoreLocation** 参数指定 **LocalMachine**。 无需指定 **CertificateStoreName** 参数，因为它默认为“My”。 使用 **X509** 引用时，证书文件必须位于群集节点上的 */var/lib/sfcerts* 目录中。  

以下 XML 显示基于此样式的 **TransportSettings** 节：

```xml
<Section Name="HelloWorldStatefulTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
    <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
    <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
</Section>
```

### <a name="using-x5092-securitycredentialstype"></a>使用 X509_2 SecurityCredentialsType

使用 Java SDK，可为 **SecurityCredentialsType** 指定 **X509_2**。 此值对应于 `SecurityCredentials` ([Java](https://docs.microsoft.com/java/api/system.fabric._security_credentials)) 类型的 `X509Credentials2` ([Java](https://docs.microsoft.com/java/api/system.fabric._x509_credentials2))。 

使用 **X509_2** 引用时，请指定路径参数，以便可以在除 */var/lib/sfcerts* 以外的目录中查找证书。  以下 XML 显示用于指定证书位置的参数： 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

以下 XML 显示基于此样式的 **TransportSettings** 节。

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

> [!NOTE]
> 在前面的 XML 中，证书指定为 .crt 文件。 这表示在同一位置还有一个包含私钥的 .key 文件。

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>配置在 Linux 群集上运行的 Reliable Services 应用

使用 Service Fabric SDK 可与 Service Fabric 运行时 API 通信，以利用平台。 在安全的 Linux 群集上运行使用此功能的任何应用程序时，需要使用一个证书来配置该应用程序，该证书可用于验证 Service Fabric 运行时。 包含使用 .NET Core 或 Java SDK 编写的 Service Fabric Reliable Service 服务的应用程序需要此配置。 

若要配置应用程序，请在 **Certificates** 标记（位于 *ApplicationManifest.xml* 文件中的 **ApplicationManifest** 标记下面）下面添加一个 [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) 元素。 以下 XML 显示按证书指纹引用的证书： 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

可以引用群集证书，或每个群集节点上安装的证书。 在 Linux 上，证书文件必须位于 */var/lib/sfcerts* 目录中。 有关详细信息，请参阅 [Linux 节点上的 X.509 证书的位置和格式](#location-and-format-of-x509-certificates-on-linux-nodes)。



