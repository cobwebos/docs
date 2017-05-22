>[!NOTE]
>对于不固定的资源，可以通过开具支持票证请求提高配额。 请**不要**创建更多的 Azure 媒体服务帐户以求获取更高的限制。

| 资源 | 默认限制 | 
| --- | --- | 
| 单个订阅中的 Azure 媒体服务 (AMS) 帐户数 | 25（固定） |
| 每个 AMS 帐户的媒体保留单位 (Ru) |25 (S1, S2)<br/>10 (S3) <sup>(1)</sup> | 
| 每个 AMS 帐户的作业数 | 50,000<sup>(2)</sup> |
| 每个作业的链接任务数 | 30（固定） |
| 每个 AMS 帐户的资产数 | 1,000,000|
| 每个任务的资产数 | 50 |
| 每个作业的资产数 | 100 |
| 一次与一个资产关联的唯一定位符数 | 5<sup>(4)</sup> |
| 每个 AMS 帐户的实时频道数 |5|
| 每个频道的停止状态节目数 |50|
| 每个频道的运行状态节目数 |3|
| 每个 AMS 帐户处于运行状态的流式处理终结点数|2|
| 每个流式处理终结点的流式处理单位数 |10 |
| 存储帐户 | 1,000<sup>(5)</sup>（固定） |
| 策略 | 1,000,000<sup>(6)</sup> |
| 文件大小| 在某些情况下，支持在媒体服务中处理的最大文件大小存在限制。 <sup>7</sup> |
  
<sup>1</sup> 印度西部不提供 S3 RU。

<sup>2</sup> 这个数字包括已排队的、已完成的、活动的和已取消的作业。 不包括已删除的作业。 可以使用 **IJob.Delete** 或 **DELETE** HTTP 请求删除旧作业。

自 2017 年 4 月 1 日起，即使记录总数低于最大配额，也将自动删除帐户中所有超过 90 天的作业记录，及其相关的任务记录。 在需要时，可使用[此处](../articles/media-services/media-services-dotnet-manage-entities.md)所述的代码存档作业/任务信息。

<sup>3</sup> 发出列出作业实体的请求时，每个请求将最多返回 1,000 个实体。 如果需要跟踪所有已提交的作业，可以使用 top/skip，如 [OData 系统查询选项](http://msdn.microsoft.com/library/gg309461.aspx)中所述。

<sup>4</sup> 定位符不用于管理按用户的访问控制。 要为不同用户提供不同的访问权限，请使用数字权限管理 (DRM) 解决方案。 有关详细信息，请参阅[此](../articles/media-services/media-services-content-protection-overview.md)部分。

<sup>5</sup> 存储帐户必须来自同一 Azure 订阅。

<sup>6</sup> 不同的 AMS 策略限制为 1,000,000 个（例如，对于定位器策略或 ContentKeyAuthorizationPolicy）。 

>[!NOTE]
> 如果经常使用相同的天数/访问权限等，则应使用相同的策略 ID。有关信息和示例，请参阅[此](../articles/media-services/media-services-dotnet-manage-entities.md#limit-access-policies)部分。

<sup>7</sup>如果要将内容上传到 Azure 媒体服务中的资产，并且意图是要使用 Microsoft 服务中的媒体处理器之一（例如，Media Encoder Standard 和 Media Encoder Premium Workflow 等编码器，或者 Face Detector 等分析引擎）对此内容进行处理，则应注意以下针对最大大小的约束条件。 

自 2017 年 5 月 15 日开始，单个 Blob 支持的最大大小为 195 TB - 如果文件大于此限制，则任务会失败。 我们正在制定解决方法来解决此限制。 另外，资产的最大大小的约束条件如下所示。

| 媒体保留单位类型 | 最大输入大小 (GB)| 
| --- | --- | 
|S1    | 325|
|S2    | 640|
|S3    | 260|
