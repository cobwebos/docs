---
title: Azure Service Fabric 反向代理安全通信
description: 在 Azure Service Fabric 应用程序中配置反向代理以启用安全的端到端通信。
ms.topic: conceptual
ms.date: 08/10/2017
ms.openlocfilehash: e88a81108f38efefe413024fb2b41bbd82f297b2
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858523"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>使用反向代理连接到安全服务

本文介绍如何在反向代理与服务之间建立安全连接，从而启用端到端的安全通道。 若要了解有关反向代理的详细信息，请参阅 [Azure Service Fabric 中的反向代理](service-fabric-reverseproxy.md)

> [!IMPORTANT]
> 仅当反向代理配置为侦听 HTTPS 时，才支持连接到安全服务。 本文假定现为这种情况。 请参阅[在 Azure Service Fabric 中设置反向代理](service-fabric-reverseproxy-setup.md)，在 Service Fabric 中配置反向代理。

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>在反向代理与服务之间建立安全连接 

### <a name="reverse-proxy-authenticating-to-services"></a>反向代理在服务中进行身份验证：
反向代理使用其证书向服务标识自己。 对于 Azure 群集，证书使用资源管理器模板的 [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [资源类型部分](../azure-resource-manager/templates/template-syntax.md)中的 reverseProxyCertificate 属性指定。 对于独立群集，证书使用 ClusterConfig.json“安全”部分中的 ReverseProxyCertificate 或 ReverseProxyCertificateCommonNames 属性指定  。 若要了解详细信息，请参阅[在独立群集上启用反向代理](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters)。 

服务可以实现逻辑来验证反向代理提供的证书。 服务可以在配置包中将已接受的客户端证书详细信息指定为配置设置。 此设置可在运行时读取，并用于验证反向代理提供的证书。 请参阅[管理应用程序参数](service-fabric-manage-multiple-environment-app-configuration.md)来添加配置设置。 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>反向代理通过服务提供的证书验证服务的身份：
反向代理支持使用以下策略对服务提供的证书执行服务器证书验证：None、ServiceCommonNameAndIssuer 和 ServiceCertificateThumbprints。
若要选择反向代理使用的策略，请在 [fabricSettings](service-fabric-cluster-fabric-settings.md) 中的 **ApplicationGateway/Http** 节下指定 **ApplicationCertificateValidationPolicy**。

下一部分介绍了其中每个选项的配置详细信息。

### <a name="service-certificate-validation-options"></a>服务证书验证选项 

- **无**：反向代理跳过代理服务证书的验证，并建立安全连接。 这是默认行为。
在 [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 节中，指定值为 **None** 的 **ApplicationCertificateValidationPolicy**。

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "None"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCommonNameAndIssuer**：反向代理根据证书公用名称和直接颁发者的指纹，验证服务提供的证书：在 [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 节中指定值为 **ServiceCommonNameAndIssuer** 的 **ApplicationCertificateValidationPolicy**。

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCommonNameAndIssuer"
                 }
               ]
             }
           ],
           ...
   }
   ```

   若要指定服务公用名称和颁发者指纹，请在 **fabricSettings** 下添加 [**ApplicationGateway/Http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) 节，如下所示。 可在 **parameters** 数组中添加多个证书公用名称和颁发者指纹对。 

   如果终结点反向代理正在连接以显示一个证书，该证书的公用名和颁发者指纹与此处指定的任何值匹配，则建立 TLS 通道。
   如果无法匹配证书详细信息，则反向代理将无法处理该客户端的请求并返回 502（错误的网关）状态代码。 HTTP 状态行也会包含短语“Invalid SSL Certificate”。 

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
               "parameters": [
                 {
                   "name": "WinFabric-Test-Certificate-CN1",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
                 },
                 {
                   "name": "WinFabric-Test-Certificate-CN2",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCertificateThumbprints**：反向代理将根据代理服务证书的指纹验证该证书。 当服务配置了自签名证书时，可以选择使用此路由：在 [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 节中，为 **ApplicationCertificateValidationPolicy** 指定值 **ServiceCertificateThumbprints**。

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCertificateThumbprints"
                 }
               ]
             }
           ],
           ...
   }
   ```

   另外，在 **ApplicationGateway/Http** 节中，指定包含 **ServiceCertificateThumbprints** 条目的指纹。 可在 value 字段中以逗号分隔列表的形式指定多个指纹，如下所示：

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                   ...
                 {
                   "name": "ServiceCertificateThumbprints",
                   "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
                 }
               ]
             }
           ],
           ...
   }
   ```

   如果此配置条目中列出了服务器证书的指纹，则反向代理将成功建立 TLS 连接。 否则，它会终止连接，无法处理客户端的请求并返回 502（错误的网关）。 HTTP 状态行也会包含短语“Invalid SSL Certificate”。

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>服务公开安全和不安全终结点时使用的终结点选择逻辑
Service Fabric 支持为服务配置多个终结点。 有关详细信息，请参阅[在服务清单中指定资源](service-fabric-service-manifest-resources.md)。

反向代理根据[服务 URI](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy) 中的 **ListenerName** 查询参数选择某个终结点来转发请求。 如果未指定 **ListenerName** 参数，则反向代理可以选取终结点列表中的任一终结点。 根据为服务配置的终结点，所选终结点可以是 HTTP 或 HTTPS 终结点。 在某些情况下，或者根据某些要求，你希望反向代理在“仅限安全模式”下运行；也就是说，你不希望安全反向代理将请求转发到不安全的终结点。 若要将反向代理设置为仅限安全模式，请在 [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 节中指定值为 **true** 的 **SecureOnlyMode** 配置条目。   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> [!NOTE]
> 在 **SecureOnlyMode** 下运行时，如果客户端已指定对应于 HTTP（不安全）终结点的 **ListenerName**，则反向代理拒绝请求，并显示 404 (Not Found) HTTP 状态代码。

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>通过反向代理设置客户端证书身份验证
TLS 终止发生在反向代理上，并且所有客户端证书数据都将丢失。 若要让服务执行客户端证书身份验证，请在 [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 节中指定 **ForwardClientCertificate** 设置。

1. 当**ForwardClientCertificate**设置为**false**时，反向代理将不会在客户端的 TLS 握手期间请求客户端证书。
这是默认行为。

2. 当**ForwardClientCertificate**设置为**true**时，反向代理在与客户端的 TLS 握手期间请求客户端的证书。
然后，将会转发名为 **X-Client-Certificate** 的自定义 HTTP 标头中的客户端证书数据。 标头值是客户端证书的 base64 编码 PEM 格式字符串。 检查证书数据后，服务可能会成功/无法处理请求并返回相应的状态代码。
如果客户端不提供证书，反向代理将转发空标头，并让服务处理该情况。

> [!NOTE]
> 反向代理仅用作转发服务。 它不会对客户端的证书执行任何验证。


## <a name="next-steps"></a>后续步骤
* [在群集上设置和配置反向代理](service-fabric-reverseproxy-setup.md)。
* 请参阅[配置反向代理以连接到安全服务](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)
* 参阅 [GitHub 上的示例项目](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)中服务之间的 HTTP 通信示例。
* [使用 Reliable Services 远程控制执行远程过程调用](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services 中使用 OWIN 的 Web API](service-fabric-reliable-services-communication-webapi.md)
* [管理群集证书](service-fabric-cluster-security-update-certs-azure.md)
