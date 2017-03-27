---
title: "在 Azure Functions 中使用数据包捕获执行主动网络监视 | Microsoft 文档"
description: "本文介绍如何使用 Azure 网络观察程序创建警报触发的数据包捕获"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6c7a0402a913c836f3248fa6f2f9b27cbf2b0d04
ms.openlocfilehash: a23d569451d2cc776b2e8fc84ec4d01259f74c63
ms.lasthandoff: 02/23/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-azure-functions"></a>在 Azure Functions 中使用数据包捕获执行主动网络监视

网络观察程序数据包捕获功能可以创建捕获会话来跟踪传入和传出虚拟机的流量。 可在捕获文件中包含一个定义的筛选器，以便只跟踪想要监视的流量。 然后，将此数据存储在存储 Blob 中或来宾计算机本地。 可以通过 Azure Functions 等其他自动化方案远程启动此功能。 数据包捕获提供基于定义的网络异常运行主动捕获的功能。 其他用途包括收集网络统计信息、获取网络入侵信息、调试客户端与服务器之间的通信，等等。

Azure 中部署的资源全天候运行， 但你或你的同事无法全天候主动监视所有资源的状态。 如果凌晨 2 点出现问题，会发生什么情况？

在 Azure 生态系统中使用网络观察程序、警报和 Functions，可以主动针对网络中的问题做出响应，并使用数据和工具来解决问题。

## <a name="before-you-begin"></a>开始之前

在本示例中，VM 发送的 TCP 段数超过平常，出现问题时你希望能够收到警报。 TCP 段只是用作示例，我们可以使用任何警报条件。 收到警报时，你希望获得数据包级别的数据来了解通信量为何提高，以便可以采取措施让计算机恢复日常通信。
本方案假设已安装网络观察程序的现有实例，并且使用一个包含有效虚拟机的资源组。

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>方案

为了自动完成此过程，我们在 VM 上创建并连接了一个发生事件时要触发的警报，同时创建了一个用于调用网络观察程序的 Azure 函数。

此方案将：

* 创建一个启动数据包捕获的 Azure 函数。
* 在虚拟机上创建警报规则
* 将警报规则配置为调用该 Azure 函数

## <a name="creating-an-azure-function-and-overview"></a>创建 Azure 函数和操作概述

第一步是创建一个 Azure 函数来处理警报并创建数据包捕获。 

以下列表了提供发生的工作流概述。

1. 在 VM 上触发警报。
1. 该警报通过 Webhook 调用 Azure 函数。
1. Azure 函数处理警报，并启动网络观察程序数据包捕获会话。
1. 数据包捕获在 VM 上运行并收集流量。 
1. 将捕获文件上载到存储帐户进行审查和诊断 

可以遵循[创建第一个 Azure 函数](../azure-functions/functions-create-first-azure-function.md)，在门户中创建 Azure 函数。 在本示例中，我们选择了 HttpTrigger-CSharp 类型函数。 

> [!NOTE]
> Azure Functions 提供其他语言，但这些语言可能是试验性的，不像 PowerShell 和 Python 等语言那样完全受支持。

## <a name="processing-the-alert-and-starting-a-packet-capture-session"></a>处理警报并启动数据包捕获会话

现在，可以从 Azure 函数内部调用网络观察程序。 根据要求，此函数的实现有所不同。 但是，代码的常规流程大致如下：

1. 处理输入参数
2. 查询现有的数据包捕获，验证限制并解决名称冲突
3. 使用适当的参数创建数据包捕获
4. 定期轮询数据包捕获，直到完成
5. 通知用户数据包捕获会话已完成

以下示例采用 C# 语言，可在 Azure 函数中使用。 需要替换订阅、客户端 ID、租户 ID 和客户端机密的某些值。

```CSharp
using System.Net;
using Newtonsoft;
using Newtonsoft.Json;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

public static TraceWriter log;
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter twlog)
{
    log = twlog;
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    HttpContent requestContent = req.Content;
    string jsonContent = requestContent.ReadAsStringAsync().Result;
    
    //Deserialize json input
    WebhookInputParameters inParams = JsonConvert.DeserializeObject<WebhookInputParameters>(jsonContent);

    log.Info($@"subscriptionId: {inParams.subscriptionId}  
                networkWatcherResourceGroup: {inParams.networkWatcherResourceGroup} 
                networkWatcherName: {inParams.networkWatcherName} 
                packetCaptureName: {inParams.packetCaptureName} 
                storageID: {inParams.storageID} 
                timeLimit: {inParams.timeLimit} 
                targetVM: {inParams.targetVM}");

    //Get JWT Token
    string token = GetAuthorizationToken();
    
    //Create URI and Delete existing Packet Capture if it exists
    string endpoint = @"https://management.azure.com";
    string PacketCaptureRequestURI = $@"{endpoint}/subscriptions/{inParams.subscriptionId}/resourceGroups/{inParams.networkWatcherResourceGroup}/providers/Microsoft.Network/networkWatchers/{inParams.networkWatcherName}/packetCaptures/{inParams.packetCaptureName}?api-version=2016-03-30";

    //Delete Packet Capture
    HttpWebRequest packetCaptureDelete = Request(PacketCaptureRequestURI, token, "Delete");
    //Errors from delete (404 not found) indicate that the packet capture does not exist already
    HttpWebResponse pcDeleteResponse = getHttpResponse(packetCaptureDelete);

    //Create Packet Capture Request Body
    PacketCaptureRequestBody pcrb = new PacketCaptureRequestBody();
    pcrb.properties.timeLimitInSeconds = inParams.timeLimit;
    pcrb.properties.target = inParams.targetVM;
    pcrb.properties.storageLocation.storageId = inParams.storageID;

    //serialize PacketCaptureRequestBody
    var jSetting = new JsonSerializerSettings();
    jSetting.NullValueHandling = NullValueHandling.Ignore;
    jSetting.Formatting = Formatting.Indented;
    string pcRequestBody = JsonConvert.SerializeObject(pcrb, jSetting);
    
    //Create Packet Capture via PUT 
    HttpWebRequest packetCaptureCreate = Request(PacketCaptureRequestURI, token, "Put", pcRequestBody);
    HttpWebResponse pcPostResponse = getHttpResponse(packetCaptureCreate);
    
    //if packet capture creation successful return to user created storagePath
    if (!(pcPostResponse == null) && pcPostResponse.StatusCode == HttpStatusCode.Created)
    {
        string pcPostResponseLocation = getHeaderContent(pcPostResponse, "Location") ?? "Header null";
        string pcPostResponseContent = httpWebResponseContent(pcPostResponse) ?? "Response Content null";

        //Deserialize response body into object
        PacketCaptureResponseBody pcrbObj = JsonConvert.DeserializeObject<PacketCaptureResponseBody>(pcPostResponseContent);
        return req.CreateResponse(HttpStatusCode.OK, $"Packet Capture successfully created and will upload to StoragePath: {pcrbObj.properties.storageLocation.storagePath}");
    }
    return req.CreateResponse(HttpStatusCode.OK, "Error creating packet capture");
}

// Creates and returns the HTTPWebRequest
public static HttpWebRequest Request(string requestURI, string token, string requestType, string requestContent = null)
{
    
    var httpWebRequest = (HttpWebRequest)WebRequest.Create(requestURI);
    httpWebRequest.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
    httpWebRequest.ContentType = "application/json";
    httpWebRequest.Method = requestType;
    if (requestContent != null)
    {
        httpWebRequest.ContentLength = (requestContent != null) ? requestContent.Length : 0;
    }
    log.Info("Sending Request: " + requestURI + (requestContent ?? ""));

    //ensure requestType is in uppercase
    requestType = requestType.ToUpper();
    if (requestType.Equals("PUT") || requestType.Equals("POST"))
    {
        using (var requestStream = new StreamWriter(httpWebRequest.GetRequestStream()))
        {
            requestStream.Write(requestContent);
        }
    }
    log.Info("Request Sent");
    return httpWebRequest;
}

public static HttpWebResponse getHttpResponse(HttpWebRequest httpWebRequest)
{
    HttpWebResponse httpWebResponse = null;
    try
    {
        httpWebResponse = (HttpWebResponse)httpWebRequest.GetResponse();
    }
    catch (Exception ex)
    {
        log.Info("Error from " + ex.Message, "HttpWebResponsexeption");
    }
    return httpWebResponse;
}

public static string httpWebResponseContent(HttpWebResponse httpWebResponse)
{
    string content = null;
    using (StreamReader reader = new StreamReader(httpWebResponse.GetResponseStream()))
    {
        content = reader.ReadToEnd();
    }
    return content;
}

public static string getHeaderContent(HttpWebResponse httpWebResponse, string param)
{
    log.Info((int)httpWebResponse.StatusCode + "\tStatus Description: " + httpWebResponse.StatusDescription + "\r\n Headers: " + httpWebResponse.Headers);
    return httpWebResponse.Headers.Get(param);
}

//Gets the JWT Token to be able to make REST calls
public static string GetAuthorizationToken()
{
    var tenantId = "<insert tenant id>"; 
    var clientId = "<insert client id>"; 
    var secret = "<insert client secret>";
    var subscriptionId = "<insert subscription id>"; 

    string authContextURL = "https://login.windows.net/" + tenantId;
    var authenticationContext = new AuthenticationContext(authContextURL);
    var credential = new ClientCredential(clientId, secret);
    var result = authenticationContext.AcquireToken(resource: "https://management.azure.com/", clientCredential: credential);
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    return result.AccessToken;
}

public class WebhookInputParameters
{
    public string subscriptionId { get; set; }
    public string networkWatcherResourceGroup { get; set; }
    public string networkWatcherName { get; set; }
    public string packetCaptureName { get; set; }
    public string storageID { get; set; }
    public int timeLimit { get; set; }
    public string targetVM { get; set; }
}

public class PacketCaptureRequestBody
{
    public PacketCaptureRequestBody()
    {
        properties = new Properties();
    }
    public Properties properties { get; set; }
}

public class StorageLocation
{
    public string storageId { get; set; }
    public string storagePath { get; set; }
    public string filePath { get; set; }
}

public class Filter
{
    public string protocol { get; set; }
    public string localIP { get; set; }
    public string localPort { get; set; }
    public string remoteIP { get; set; }
    public string remotePort { get; set; }
}

public class Properties
{
    public Properties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }

    public string target { get; set; }
    public int bytestToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }

    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
}

public class PacketCaptureResponseBody
{
    public PacketCaptureResponseBody()
    {
        properties = new PacketCaptureResponseProperties();
    }
    public string name { get; set; }
    public string id { get; set; }
    public string etag { get; set; }
    public PacketCaptureResponseProperties properties { get; set; }
}

public class PacketCaptureResponseProperties
{
    public PacketCaptureResponseProperties()
    {
        filters = new List<Filter>();
        storageLocation = new StorageLocation();
    }
    public string provisioningState { get; set; }
    public string target { get; set; }
    public int bytesToCapturePerPacket { get; set; }
    public int totalBytesPerSession { get; set; }
    public int timeLimitInSeconds { get; set; }
    public StorageLocation storageLocation { get; set; }
    public List<Filter> filters { get; set; }
    public string captureStartTime { get; set; }
    public string packetCaptureStatus { get; set; }
    public List<object> packetCaptureError { get; set; }
}
``` 

创建函数后，需要将警报配置为调用与该函数相关联的 URL。 若要获取此值，请从 Function App 中复制函数 URL。

![查找函数 URL][2]

如果需要在 Webhook POST 请求的有效负载中使用自定义属性，请参阅[针对 Azure 指标警报配置 Webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="configure-an-alert-on-a-vm"></a>在 VM 上配置警报

可以配置警报，以便在特定的指标超过分配的阈值时通知相关人员。 在本示例中，警报是针对 TCP 段发送的，但也可以从其他许多指标触发该警报。 在本示例中，已将某个警报配置为调用 Webhook 来调用函数。

### <a name="create-the-alert-rule"></a>创建警报规则

导航到现有虚拟机并添加警报规则。 有关配置警报的更详细文档，请参阅 [User Azure portal to create alerts for Azure services](../monitoring-and-diagnostics/insights-alerts-portal.md)（在用户 Azure 门户中为 Azure 服务创建警报）。 

![将 VM 警报规则添加到虚拟机][1]

> [!NOTE]
> 某些指标默认未启用。 请访问[启用监视和诊断](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md)，详细了解如何启用其他指标

最后，将前面步骤中获取的 URL 粘贴到警报中的 Webhook 文本框。 单击“确定”保存警报规则。

![将 URL 粘贴到警报规则][3]

## <a name="downloading-and-viewing-the-capture-file"></a>下载并查看捕获文件

如果将捕获保存到了存储帐户，可以通过门户或以编程方式下载捕获文件。 如果捕获文件存储在本地，可以通过登录到虚拟机来检索捕获文件。 

有关从 Azure 存储帐户下载文件的说明，请参阅[通过 .NET 开始使用 Azure Blob 存储](../storage/storage-dotnet-how-to-use-blobs.md)。 可以使用的另一个工具是存储资源管理器。 有关存储资源管理器的详细信息可以在此链接中找到：[存储资源管理器](http://storageexplorer.com/)

下载捕获后，可以使用能够读取 **.cap** 文件的任何工具来查看捕获。 下面提供了其中两个工具的链接：

[Microsoft Message Analyzer](https://technet.microsoft.com/en-us/library/jj649776.aspx)  
[WireShark](https://www.wireshark.org/)  

## <a name="next-steps"></a>后续步骤

访问 [Packet capture analysis with Wireshark](network-watcher-alert-triggered-packet-capture.md)（使用 Wireshark 分析数据包捕获），了解如何查看数据包捕获

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png

