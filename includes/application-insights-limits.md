每个应用程序（即每个检测密钥）的度量值和的事件数都具有一定限制。 

限制取决于你选择的[定价计划](https://azure.microsoft.com/pricing/details/application-insights/)。

| **资源** | **默认限制** | **注意**
| --- | --- | --- |
| 每日的总数据量 | 500 GB | 可以通过设置一个上限来减少数据量。 如果需要更多，请发送邮件至 AIDataCap@microsoft.com 
| 每月免费数据量<br/> （基本价格计划） | 1 GB | 额外的数据按 GB 收费
| 限制 | 16000 个事件/秒 | 一分钟计量值。 
| 数据保留 | 90 天 | 对于[搜索](../articles/application-insights/app-insights-diagnostic-search.md)、[分析](../articles/application-insights/app-insights-analytics.md)和[指标资源管理器](../articles/application-insights/app-insights-metrics-explorer.md)
| [可用性多步骤测试](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests)详细结果保留 | 90 天 | 每个步骤的详细结果
| [属性](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)和[指标](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)<sup>2</sup> 名称计数 | 200 | 
| 属性和指标名称长度 | 150 |
| 属性值字符串长度 | 8192 |
| 属性的不同值<sup>3,4</sup> | 100 | >100 => 无法在指标资源管理器中将属性用作筛选器
| 跟踪和异常消息长度 | 10000 |
| 每个应用的[可用性测试](../articles/application-insights/app-insights-monitor-web-app-availability.md)计数  | 10 |

1. 所有这些数字都是按检测键统计的。
2. 指标名称同时在 TrackMetric 中和其他 Track*() 调用的度量参数中进行定义。 指标名称按检测键全局可用。
3. 只有当每个属性的不同值少于 100 个时，才能将属性用作筛选器和分组依据。 当不同值的数目超过 100 时，仍然可以搜索属性，但无法再将其用作筛选器或分组依据。
4. 标准属性（例如请求名称和页面 URL）限制为每周 1000 个不同值。 在达到 1000 个不同值后，额外的值将被标记为“其他值”。 原始值仍然可以用于全文搜索和筛选。


[关于 Application Insights 中的定价和配额](../articles/application-insights/app-insights-pricing.md)

<!--HONumber=Jan17_HO3-->


