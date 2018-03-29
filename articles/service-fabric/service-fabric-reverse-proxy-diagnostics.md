---
title: Azure Service Fabric 反向代理诊断 | Microsoft Docs
description: 了解如何监视和诊断在反向代理处处理的请求。
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: e645d86e4527cab1e650575c4d9a10dbd4b9de4e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>了解如何监视和诊断在反向代理处处理的请求

从 5.7 版本的 Service Fabric 开始，可收集反向代理事件。 事件采用两个通道提供，一个只包含与反向代理处请求处理故障相关的错误事件，而另一个通道包含关于成功和失败请求记录的详细事件。

若要收集本地和 Azure Service Fabric 群集中这些通道的事件，请参阅[收集反向代理事件](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)。

## <a name="troubleshoot-using-diagnostics-logs"></a>使用诊断日志进行故障排除
下面举例说明了如何解释人们可能遇到的常见失败日志：

1. 反向代理将返回响应状态代码 504（超时）。

    原因之一可能是由于服务在请求超时时间段内无法答复。
下面的第一个事件记录了在反向代理处接收到的请求的详细信息。 第二个事件指示在向服务转发时请求失败，原因是“内部错误 = ERROR_WINHTTP_TIMEOUT” 

    有效负载包括：

    *  **traceId**：可将此 GUID 用于关联与单个请求对应的所有事件。 在下面的两个事件中，traceId = 2f87b722-e254-4ac2-a802-fd315c1a0271 暗示它们属于同一请求。
    *  **requestUrl**：请求已发送到的 URL（反向代理 URL）。
    *  **verb**：HTTP 谓词。
    *  **remoteAddress**：发送请求的客户端地址。
    *  **resolvedServiceUrl**：向其解析传入请求的服务终结点 URL。 
    *  **errorDetails**：关于失败的详细信息。

    ```
    {
      "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
      "ProviderName": "Microsoft-ServiceFabric",
      "Id": 51477,
      "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
      "ProcessId": 57696,
      "Level": "Informational",
      "Keywords": "0x1000000000000021",
      "EventName": "ReverseProxy",
      "ActivityID": null,
      "RelatedActivityID": null,
      "Payload": {
        "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
        "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
        "verb": "GET",
        "remoteAddress": "::1",
        "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
        "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
      }
    }

    {
      "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
      ...
      "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
      ...
      "Payload": {
        "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
        "statusCode": 504,
        "description": "Reverse Proxy Timeout",
        "sendRequestPhase": "FinishSendRequest",
        "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
      }
    }
    ```

2. 反向代理将返回响应状态代码 404（未找到）。 
    
    下面的示例中的反向代理将返回 404，因为找不到匹配的服务终结点。
    此处需要了解的有效负载项为：
    *  **processRequestPhase**：指示发生故障时请求处理的阶段，TryGetEndpoint， 即尝试获取要转发到的服务终结点时。 
    *  **errorDetails**：列出终结点搜索条件。 此处，你可看到指定的 listenerName = FrontEndListener，而副本终结点列表仅包含名称为 OldListener 的侦听程序。
    
    ```
    {
      ...
      "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
      "ProcessId": 57696,
      "Level": "Warning",
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
        "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
        ...
        "processRequestPhase": "TryGetEndoint",
        "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
      }
    }
    ```
    反向代理返回“404 未找到”的另一个示例为：ApplicationGateway\Http 配置参数 SecureOnlyMode 已设为 true，同时反向代理正在侦听 HTTPS，但所有副本终结点都不安全（侦听 HTTP）。
    反向代理返回 404，因为找不到用于侦听 HTTPS 的终结点来转发请求。 分析事件负载中的参数有助于缩小问题范围：
    
    ```
        "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
    ```

3. 反向代理请求失败，出现超时错误。 
    事件日志包含一个具有已接收请求详细信息（此处未显示）的事件。
    下一个事件显示服务收到 404 状态代码响应，反向代理将启动重新解析。 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    收集所有事件时，你将看到一个关于显示每个解析和转发尝试的事件的练习。
    序列中的最后一个事件显示请求处理失败，出现超时，以及解析尝试成功的次数。
    
    > [!NOTE]
    > 建议将详细通道事件收集默认设置为禁用，并根据需要启用进行故障排除。

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    如果仅针对严重/错误事件启用收集，你会发现有一个事件将包含超时详细信息和解析尝试次数。 
    
    想要向用户发送回 404 状态代码的服务应在响应中添加一个“X-ServiceFabric”标头。 将该标头添加到响应后，反向代理会将状态代码转发回客户端。  

4. 客户端与请求断开连接时的情况。

    当反向代理要将响应转发给客户端但客户端断开连接时，将记录以下事件：

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```
5. 反向代理返回 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    如果未为服务清单中的服务终结点指定 URI 方案，则返回 FABRIC_E_SERVICE_DOES_NOT_EXIST 错误。

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    若要解决此问题，请在清单中指定 URI 方案。
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> 当前不记录与 websocket 请求处理相关的事件。 下一个版本将添加此功能。

## <a name="next-steps"></a>后续步骤
* [使用 Microsoft Azure 诊断的事件聚合和收集](service-fabric-diagnostics-event-aggregation-wad.md)，用于启用 Azure 群集中的日志收集。
* 若要在 Visual Studio 中查看 Service Fabric 事件，请参阅[本地监视和诊断](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。
* 请参阅[将反向代理配置为连接到安全服务](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services)了解 Azure 资源管理器模板示例，使用其他服务证书验证选项配置安全反向代理。
* 若要了解详细信息，请参阅 [Service Fabric 反向代理](service-fabric-reverseproxy.md)。
