---
title: "Azure Service Fabric 反向代理安全通信 | Microsoft Docs"
description: "将反向代理配置为启用安全端到端通信。"
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 89102e8b7bc01768742ed3e5e2bd8a9fd6c62ee8
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017

---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>使用反向代理连接到安全服务

本文介绍了如何在反向代理和服务之间建立安全连接，从而启用端到端安全通道。

只有将反向代理配置为侦听 HTTPS 时，才支持连接到安全服务。 文档的其余部分假设是这种情况。
请参阅 [Azure Service Fabric 中的反向代理](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)，在 Service Fabric 中配置反向代理。

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>在反向代理和服务之间建立安全连接 

### <a name="reverse-proxy-authenticating-to-services"></a>向服务进行反向代理身份验证：
反向代理使用证书向服务标识自身，群集[资源类型部分](../azure-resource-manager/resource-group-authoring-templates.md)的 reverseProxyCertificate 属性中指定该证书。 服务可以通过实现逻辑验证反向代理提供的证书。 服务可以将接受的客户端证书详细信息指定为配置包中的配置设置。 此信息可在运行时读取，并可用于验证反向代理提供的证书。 若要添加配置设置，请参阅[管理应用程序参数](service-fabric-manage-multiple-environment-app-configuration.md)。 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>反向代理通过服务提供的证书验证服务标识：
若要对服务提供的证书执行服务器证书验证，反向代理支持以下选项之一：无、ServiceCommonNameAndIssuer 和 ServiceCertificateThumbprints。
若要选择三个选项的其中一个，请在 [fabricSettings](service-fabric-cluster-fabric-settings.md) 下 ApplicationGateway/Http 元素的参数部分中指定 ApplicationCertificateValidationPolicy。

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

请参阅下一部分了解每个选项的其他配置的详细信息。

### <a name="service-certificate-validation-options"></a>服务证书验证选项 

- 无：反向代理跳过对代理服务证书的验证，并建立安全连接。 此选项为默认行为。
在 ApplicationGateway/Http 元素的参数部分中，将 ApplicationCertificateValidationPolicy 的值指定为“无”。

- ServiceCommonNameAndIssuer：反向代理根据证书公用名称和即时颁发者指纹，验证服务提供的证书：在 ApplicationGateway/Http 元素的参数部分中，将 ApplicationCertificateValidationPolicy 的值指定为 ServiceCommonNameAndIssuer。

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

若要指定服务公用名称和颁发者指纹，请在 fabricSettings 下添加 ApplicationGateway/Http/ServiceCommonNameAndIssuer 元素，如下所示。 参数数组元素中可以添加多个证书公用名称和颁发者指纹对。 

如果连接终结点反向代理后提供的证书的公用名称和颁发者指纹与此处指定的任何值匹配，则建立 SSL 通道。 如果与证书详细信息不匹配，则反向代理拒绝客户端请求，并显示 502（网关错误）状态代码。 并且 HTTP 状态行还会显示“无效 SSL 证书”短语。 

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


- ServiceCertificateThumbprints：反向代理根据指纹验证代理服务证书。 使用自签名证书配置服务时可以选择此方法：在 ApplicationGateway/Http 元素的参数部分中，将 ApplicationCertificateValidationPolicy 的值指定为 ServiceCertificateThumbprints。

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

并在 ApplicationGateway/Http 元素下的参数部分中，使用 ServiceCertificateThumbprints 项指定指纹。 可将多个指纹指定为值字段中的逗号分隔列表，如下所示：

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

如果此配置项中列出了服务器证书的指纹，则反向代理的 SSL 连接成功。 否则将终止连接，拒绝客户端请求，并显示 502（网关错误）。 并且 HTTP 状态行还会显示“无效 SSL 证书”短语。

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>服务同时公开安全和不安全终结点时的终结点选择逻辑
Service Fabric 支持为一个服务配置多个终结点。 请参阅[在服务清单中指定资源](service-fabric-service-manifest-resources.md)。

反向代理根据 ListenerName 查询参数选择其中一个终结点来转发请求。 如果未指定该参数，则选择终结点列表中的任意一个终结点。 在本示例中可以是 HTTP 或 HTTPS 终结点。 有时可能希望/需要在“仅安全模式”下运行反向代理，即 不希望安全反向代理将请求转发给不安全终结点。 在 ApplicationGateway/Http 元素的参数部分中，将 SecureOnlyMode 配置项的值指定为 true 可以实现这一目的。   

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

> 
> 在 SecureOnlyMode 下运行时，如果客户端已指定对应于 HTTP（不安全）终结点的 ListenerName，则反向代理拒绝请求，并显示 404 (Not Found) HTTP 状态代码。

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>通过反向代理设置客户端证书身份验证
反向代理和所有客户端证书数据丢失时，SSL 将终止。 若要让服务执行客户端证书身份验证，请在 ApplicationGateway/Http 元素的参数部分中设置 ForwardClientCertificate 设置。

1. 如果将 ForwardClientCertificate 设置为 false，其 SSL 与客户端握手期间，反向代理不会请求客户端证书。
此选项为默认行为。

2. 如果将 ForwardClientCertificate 设置为 true，其 SSL 与客户端握手期间，反向代理将请求客户端证书。
然后会转发名为 X-Client-Certificate 的自定义 HTTP 标头中的客户端证书数据。 标头值是客户端证书的 base64 编码 PEM 格式字符串。 检查证书数据后，服务可以接受/拒绝请求，并显示相应状态代码。
如果客户端不提供证书，反向代理将转发空标头，并让服务处理该情况。

> 反向代理只是一个转发程序。 它不会对客户端证书执行验证。


## <a name="next-steps"></a>后续步骤
* 请参阅[将反向代理配置为连接到安全服务](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services)了解 Azure Resource Manager 模板示例，使用其他服务证书验证选项配置安全反向代理。
* 参阅 [GitHub 上的示例项目](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)中服务之间的 HTTP 通信示例。
* [使用 Reliable Services 远程控制执行远程过程调用](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services 中使用 OWIN 的 Web API](service-fabric-reliable-services-communication-webapi.md)
* [管理群集证书](service-fabric-cluster-security-update-certs-azure.md)

