有一些限制的度量值和每个应用程序的事件数 (即，每个检测键)。 限制取决于[定价计划](https://azure.microsoft.com/pricing/details/application-insights/)你选择。

| **资源** | **默认限制** | **注意**
| --- | --- | --- |
| 每日的总数据 | 500 GB | 你可以通过设置 cap 减少数据。 如果你需要更多，邮件AIDataCap@microsoft.com。
| 每月的免费数据<br/> （基本价格计划） | 1 GB | 其他数据负责每千兆字节。
| 限制 | 32k 事件/秒 | 限制为一分钟内单位。
| 数据保留 | 90 天 | 此资源被用于[搜索](../articles/application-insights/app-insights-diagnostic-search.md)，[分析](../articles/application-insights/app-insights-analytics.md)，和[指标资源管理器](../articles/application-insights/app-insights-metrics-explorer.md)。
| [可用性多步骤测试](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests)详细结果保留 | 90 天 | 此资源提供了每个步骤的详细的结果。
| 最大事件大小 | 64K | 
| 属性和度量值名称长度 | 150 | 请参阅[键入架构](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)
| 属性值字符串长度 | 8,192 | 请参阅[键入架构](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)
| 跟踪和异常消息长度 | 10k | 请参阅[键入架构](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/)
| [可用性测试](../articles/application-insights/app-insights-monitor-web-app-availability.md)每个应用程序的计数  | 10 |
| [探查器](../articles/application-insights/app-insights-profiler.md)数据保留期 | 5 天 |
| [探查器](../articles/application-insights/app-insights-profiler.md)每天发送的数据 | 10 GB |

有关详细信息，请参阅[有关定价和 Application Insights 中的配额](../articles/application-insights/app-insights-pricing.md)。

