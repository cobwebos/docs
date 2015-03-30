<properties 
	pageTitle="如何使用 Azure Media Encoder 对资产进行编码" 
	description="了解如何使用 Azure Media Encoder 为 Media Services 上的媒体内容编码。代码示例使用 REST API。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>











# 如何使用 Azure Media Encoder 对资产进行编码

本文是 [Media Services 视频点播工作流](/zh-cn/documentation/articles/media-services-video-on-demand-workflow) 系列教程的一部分。 

## 概述
要通过 Internet 传送数字视频，你必须对媒体进行压缩。数字视频文件相当大，可能因过大而无法通过 Internet 传送或者无法在你客户的设备上正常显示。编码是压缩视频和音频以便你的客户能够查看媒体的过程。

编码作业是 Media Services 中最常见的处理操作之一。可通过创建编码作业将媒体文件从一种编码转换为另一种编码。进行编码时，可以使用 Media Services 内置的 Media Encoder。你也可以使用 Media Services 合作伙伴提供的编码器；可通过 Azure Marketplace 获取第三方编码器。可以使用为编码器定义的预设字符串或预设配置文件来指定编码任务的详细信息。若要查看可用预设的类型，请参阅"Azure Media 服务的任务预设"。如果你使用了第三方编码器，那么你应该[验证你的文件](https://msdn.microsoft.com/zh-cn/library/azure/dn750842.aspx)。

建议将夹层文件编码为自适应比特率 MP4 集，然后使用[动态打包](https://msdn.microsoft.com/zh-cn/library/azure/jj889436.aspx)将该集转换为所需的格式。

## 创建包含单个编码任务的作业 

>[WACOM.NOTE] 使用 Media Services REST API 时，需注意以下事项：
>
>访问 Media Services 中的实体时，必须在 HTTP 请求中设置特定标头字段和值。有关详细信息，请参阅 [Media Services REST API 开发的设置](/zh-cn/documentation/articles/media-services-rest-how-to-use)。

>在成功连接到 https://media.chinacloudapi.cn 之后，你将接收到指定另一个 Media Services URI 的 301 重定向。必须根据[使用 REST API 连接到 Media Services](/zh-cn/documentation/articles/media-services-rest-connect_programmatically/) 中所述对新的 URI 执行后续调用。 


以下示例说明了如何使用一个任务集来创建和发布一个作业，从而以特定分辨率和质量来编码某个视频。使用 Azure Media Encoder 编码时，可以使用[此处](https://msdn.microsoft.com/zh-cn/library/azure/dn619389.aspx)指定的任务配置预设。
	
请求：

	POST https://media.chinacloudapi.cn/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.chinacloudapi.cn%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336802231&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.chinacloudapi.cn%2f&HMACSHA256=gR%2fNaIZgolFLxBOmfSECrp16Mp0Mti3KoePVjBUCzls%3d
	Host: media.chinacloudapi.cn
	Content-Length: 476
	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.chinacloudapi.cn/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Broadband 720p", "MediaProcessorId" : "nb:mpid:UUID:70bdc2c3-ebf4-42a9-8542-5afc1e55d217",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

响应：
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1017
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://media.chinacloudapi.cn/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')
	Server: Microsoft-IIS/7.5
	x-ms-request-id: d2052a71-95b1-4a52-9420-ccca1202a5fb
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Fri, 11 May 2012 21:32:40 GMT
	
	{"d":{"__metadata":{"id":"https://media.chinacloudapi.cn/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')","uri":"https://media.chinacloudapi.cn/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"},"Tasks":{"__deferred":{"uri":"https://media.chinacloudapi.cn/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/Tasks"}},"OutputMediaAssets":{"__deferred":{"uri":"https://media.chinacloudapi.cn/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/OutputMediaAssets"}},"InputMediaAssets":{"__deferred":{"uri":"https://media.chinacloudapi.cn/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/InputMediaAssets"}},"Id":"nb:jid:UUID:40dc7bef-6bd9-2247-9f3d-d80bc257d715","Name":"NewTestJob","Created":"\/Date(1336771959431)\/","LastModified":"\/Date(1336771959431)\/","EndTime":null,"Priority":0,"RunningDuration":0,"StartTime":null,"State":0,"TemplateId":null}}

## 创建包含连锁任务的作业

在许多应用程序方案中，开发人员希望创建一系列处理任务。在 Media Services 中，可以创建一系列连锁任务。每个任务执行不同的处理步骤，并且可以使用不同的媒体处理器。连锁任务可以将资产从一个任务转给另一个任务，从而对资产执行线性序列的任务。但是，在作业中执行的任务不需要处于序列中。创建连锁任务时，连锁 **ITask** 对象在单个 **IJob** 对象中创建。

>[WACOM.NOTE] 每个作业当前有 30 个任务的限制。如果需要链接超过 30 个的任务，请创建多个作业以包含任务。


	POST https://media.chinacloudapi.cn/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.chinacloudapi.cn%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336802231&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.chinacloudapi.cn%2f&HMACSHA256=gR%2fNaIZgolFLxBOmfSECrp16Mp0Mti3KoePVjBUCzls%3d

	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://testrest.chinacloudapp.cn/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}


## 后续步骤
了解如何创建一个对资产进行编码的作业后，请转到[如何使用 Media Services 检查作业进度](/zh-cn/documentation/articles/media-services-rest-check-job-progress/) 主题。


[编码器预设]: http://msdn.microsoft.com/library/dn619392.aspx
[如何：获取媒体处理器实例]:/zh-cn/documentation/articles/media-services-get-media-processor/
[如何：上载已加密的资产]:/zh-cn/documentation/articles/media-services-create-encrypted-asset-upload-storage/
[如何检查作业进度]:/zh-cn/documentation/articles/media-services-check-job-progress/
[Azure Media Packager 的任务预设]:http://msdn.microsoft.com/library/azure/hh973635.aspx
