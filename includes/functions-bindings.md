| 类型 | 服务 | 触发器* | 输入 | 输出 |  
| --- | --- | --- | --- | --- |  
| [计划](../articles/azure-functions/functions-bindings-timer.md)  |Azure Functions |✔ | | |  
| [HTTP（REST 或 Webhook）](../articles/azure-functions/functions-bindings-http-webhook.md) |Azure Functions |✔ |  |✔\** |  
| [Blob 存储](../articles/azure-functions/functions-bindings-storage-blob.md) |Azure 存储 |✔ |✔ |✔ |  
| [事件](../articles/azure-functions/functions-bindings-event-hubs.md) |Azure 事件中心 |✔ | |✔ |  
| [队列](../articles/azure-functions/functions-bindings-storage-queue.md) |Azure 存储 |✔ | |✔ |  
| [队列和主题](../articles/azure-functions/functions-bindings-service-bus.md) |Azure 服务总线 |✔ | |✔ |  
| [存储表](../articles/azure-functions/functions-bindings-storage-table.md) |Azure 存储 | |✔ |✔ |  
| [SQL 表](../articles/azure-functions/functions-bindings-mobile-apps.md) |Azure 移动应用 | |✔ |✔ |  
| [NoSQL DB](../articles/azure-functions/functions-bindings-documentdb.md) | Azure Cosmos DB |✔ |✔ |✔ |  
| [推送通知](../articles/azure-functions/functions-bindings-notification-hubs.md) |Azure 通知中心 | | |✔ |  
| [Twilio SMS 文本](../articles/azure-functions/functions-bindings-twilio.md) |Twilio | | |✔ |
| [SendGrid 电子邮件](../articles/azure-functions/functions-bindings-sendgrid.md) | SendGrid | | |✔ |
| [Excel 表](../articles/azure-functions/functions-bindings-microsoft-graph.md) | Microsoft Graph | |✔ |✔ |
| [OneDrive 文件](../articles/azure-functions/functions-bindings-microsoft-graph.md) | Microsoft Graph | |✔ |✔ |
| [Outlook 电子邮件](../articles/azure-functions/functions-bindings-microsoft-graph.md) | Microsoft Graph | | |✔ |
| [Microsoft Graph 事件](../articles/azure-functions/functions-bindings-microsoft-graph.md) | Microsoft Graph |✔ |✔ |✔ |
| [身份验证令牌](../articles/azure-functions/functions-bindings-microsoft-graph.md) | Microsoft Graph | |✔ | |

（\* - 所有触发器都有关联的输入数据）

（\** - HTTP 输出绑定需要 HTTP 触发器）


