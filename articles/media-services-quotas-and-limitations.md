<properties pageTitle="Media Services 配额和限制" description="本主题介绍与 Microsoft Azure Media Services 关联的配额和限制。." services="media-services" documentationCenter="" authors="Juliako" manager="dwrede" />



# 配额和限制

本主题介绍与 Microsoft Azure Media Services 关联的配额和限制。

## 配额和限制

- Media Services 帐户中允许的最大资产数：1,000,000。 

- 每个作业的最大链接任务数：30。

- 任务中允许的最大资产数：每个任务 50 个资产。
 
- 每个作业的最大资产数：100。
 
- 这个数字包括已排队的、已完成的、活动的和已取消的作业。不包括已删除的作业。
 
- 帐户中的最大作业数不应超过 50,000。
 
- 可以使用 **IJob.Delete** 或 **DELETE** HTTP 请求删除旧作业。有关详细信息，请参阅 [Azure Media Encoder 的作业记录限制](http://blogs.msdn.com/b/randomnumber/archive/2014/05/05/job-record-limit-for-windows-azure-media-encoder.aspx)和[管理资产](https://msdn.microsoft.com/zh-cn/library/azure/dn642436.aspx)。 
 
- 发出列出作业实体的请求时，每个请求将最多返回 1,000 个实体。如果需要跟踪所有已提交的作业，可以使用 top/skip，如 [OData 系统查询选项](http://msdn.microsoft.com/library/gg309461.aspx)中所述。  


- 一项给定的资产一次最多只能与 5 个唯一的定位符相关联。 
	
	**注意**
	定位符不用于管理按用户的访问控制。要为不同用户提供不同的访问权限，请使用数字权限管理 (DRM) 解决方案。

- 你在单个订阅中最多只能有 25 个 Media Services 帐户。

- Media Services 限制机制会限制那些发出过多服务请求的应用程序的资源使用情况。该服务可能返回"服务不可用"(503) HTTP 状态代码。有关详细信息，请参阅 [Azure Media Services Error Codes](http://msdn.microsoft.com/library/azure/dn168949.aspx)主题中 503 错误的说明。

- 默认情况下，最多可以向 Media Services 帐户添加 5 个实时频道。 

	此外，最多可以同时启动 5 个频道。若要请求更高的限制，请参阅后面的 *如何为可更新的配额请求更高的限制*部分。

- 默认情况下，最多可以向单个频道添加 50 个节目（处于停止状态）。 

	最多只能同时添加 3 个处于运行状态的节目。若要请求更高的限制，请参阅后面的 *如何为可更新的配额请求更高的限制*部分。

- 默认情况下，每个 Media Services 帐户最多可以包含 2 个流式处理终结点（来源）。 

	此外，你最多可以同时包含 2 个处于运行状态的流式处理终结点。

	最多可以将每个流式处理终结点扩展到 10 个流式处理单位。若要请求更高的限制，请参阅后面的 *如何为可更新的配额请求更高的限制*部分。


- 默认情况下，每个 Media Services 帐户最多可以扩展到 25 个编码单元。有关详细信息，请参阅"如何缩放 Media Services"。若要请求更高的限制，请参阅"如何为可更新的配额请求更高的限制"。
	
	**重要说明**
	不要通过创建更多 Media Services 帐户来提高限制，而是应提交支持票证。

## 如何为可更新的配额请求更高的限制

### 可更新的配额

你可以通过开具支持票证，来请求更新以下配额的限制。
- 编码单元

- 实时频道（处于已停止和正在运行状态）
 
- 流式处理终结点（处于已停止和正在运行状态）
 
- 流式处理单位

### 开具支持票证

若要开具支持票证，请执行以下操作：

1. 单击[获取支持](https://manage.windowsazure.cn/?getsupport=true)。如果你尚未登录，系统将提示你输入凭据。

1. 选择你的订阅。
 
1. 在支持类型下，选择"技术"。
 
1. 单击"创建票证"。 
 
1. 在下一页显示的产品列表中选择"Azure Media Services"。
 
1. 选择适合你问题的"问题类型"。
 
1. 单击"继续"。
 
1. 根据下一页上的说明操作，然后输入有关所需编码保留单元数或流式处理保留单元数的详细信息。 
 
1. 单击"提交"以创建该票证。
 
