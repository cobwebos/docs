<properties 
	pageTitle="如何创建媒体处理器 - Azure" 
	description="了解如何创建一个媒体处理器组件用来为 Azure Media Services 编码、转换格式、加密或解密媒体内容。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>











# 如何：获取媒体处理器实例

本文是 [Media Services 视频点播工作流](/zh-cn/documentation/articles/media-services-video-on-demand-workflow) 系列教程的 一部分。 

## 概述

在 Media Services 中，媒体处理器是完成特定处理任务（例如，对媒体内容进行编码、格式转换、加密或解密）的组件。通常，当你创建一个任务以便对媒体内容进行编码、加密或格式转换时，就需要创建一个媒体处理器。

下表提供了每个可用媒体处理器的名称和说明。

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>媒体处理器名称</th>
       <th>说明</th>
	<th>更多信息</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Azure Media Encoder</td>
       <td>让你使用 Media Encoder 运行编码任务。</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx">Azure Media Encoder 的任务预设字符串</a></td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Packager</td>
        <td>让你将媒体资产从 .mp4 格式转换为平滑流式处理格式。还可让你将媒体资产从平滑流式处理格式转换为 Apple HTTP 实时流 (HLS) 格式。</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Azure Media Packager 的任务预设字符串</a></td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Encryptor</td>
        <td>让你使用 PlayReady 保护加密媒体资产。</td>
        <td><a href="http://msdn.microsoft.com/library/hh973610.aspx">Azure Media Packager 的任务预设字符串</a></td>
    </tr>
    <tr>
        <td>Azure Media Indexer</td>
        <td>使媒体文件和内容可搜索，以及生成隐藏字幕跟踪和关键字。</td>
		<td>不适用</td>
    </tr>
    <tr>
        <td>存储解密</td>
        <td>让你解密使用存储加密技术加密的媒体资产。</td>
		<td>不适用</td>
    </tr>  </tbody>
</table>

<br />

## 获取 MediaProcessor

>[AZURE.NOTE] 使用 Media Services REST API 时，需注意以下事项：
>
>访问 Media Services 中的实体时，必须在 HTTP 请求中设置特定标头字段和值。有关详细信息，请参阅 [Media Services REST API 开发的设置](/zh-cn/documentation/articles/media-services-rest-how-to-use)。

>在成功连接到 https://media.chinacloudapi.cn 之后，你将接收到指定另一个 Media Services URI 的 301 重定向。必须根据[使用 REST API 连接到 Media Services](/zh-cn/documentation/articles/media-services-rest-connect_programmatically/) 中所述对新的 URI 执行后续调用。 



以下 REST 调用演示了如何按名称获取媒体处理器实例（在本例中为 **Azure Media Encoder**）。 

	
请求：

	GET https://media.chinacloudapi.cn/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.chinacloudapi.cn%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423635565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.chinacloudapi.cn%2f&HMACSHA256=6zwXEn7YJzVJbVCNpqDUjBLuE5iUwsdJbWvJNvpY3%2b8%3d
	x-ms-version: 2.8
	Host: media.chinacloudapi.cn
	
响应：
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 273
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 8a291764-4ed7-405d-aa6e-d3ebabb0b3f6
	request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	x-ms-request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 11 Feb 2015 00:19:56 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.chinacloudapp.cn/api/$metadata#MediaProcessors","value":[{"Id":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609","Description":"Azure Media Encoder","Name":"Azure Media Encoder","Sku":"","Vendor":"Microsoft","Version":"4.4"}]}


## 后续步骤
了解如何获取媒体处理器实例后，请转到[如何为资产编码][]主题，其中说明了如何使用 Azure Media Encoder 对资产进行编码。

[如何为资产编码]: /zh-cn/documentation/articles/media-services-rest-encode-asset/
[Azure Media Encoder 的任务预设字符串]: http://msdn.microsoft.com/library/jj129582.aspx
[如何：以编程方式连接到 Media Services]: /zh-cn/documentation/articles/media-services-rest-connect_programmatically/
