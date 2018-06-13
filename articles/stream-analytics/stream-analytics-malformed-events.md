---
title: 对 Azure 流分析中格式不正确的输入事件进行故障排除 | Microsoft Docs
description: 如何知道输入数据中的哪个事件导致了流分析作业中的问题
keywords: ''
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/19/2018
ms.author: sngun
ms.openlocfilehash: 2b4f82bae20c3cb398848a89715bfdc1316e1ba1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2018
ms.locfileid: "29766376"
---
# <a name="troubleshoot-for-malformed-input-events"></a>对格式不正确的输入事件进行故障排除

当流分析作业的输入流包含格式不正确的消息时，它会导致序列化问题。 格式不正确的消息包括不正确的序列化，例如，JSON 对象中缺少括号，或者时间戳格式不正确。 当流分析作业收到格式不正确的消息时，它会丢弃消息并通过警告来通知用户。 流分析作业的“输入”标题上会显示一个警告符号：

![“输入”磁贴](media/stream-analytics-malformed-events/inputs_tile.png)

## <a name="troubleshooting-steps"></a>疑难解答步骤

1. 导航到输入磁贴，然后单击以查看警告。
2. 输入详细信息磁贴会显示一组有关问题的详细信息的警告。 下面是一个示例警告消息，警告消息显示了格式不正确的 JSON 数据所在的分区、偏移量和序列号。 

   ![包含偏移量的警告消息](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. 若要获取格式不正确的 JSON 数据，请运行下面的代码片段。 此代码块读取分区 Id、偏移量并打印数据。 可以从 [GitHub 示例存储库](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)获取完整示例。 在阅读数据后，你可以分析并更正序列化格式。

```csharp
static void PrintMessages(string partitionId, long offset, int numberOfEvents)
        {
            EventHubReceiver receiver;
            
            try
            {
                foreach (var e in receiver.Receive(numberOfEvents, TimeSpan.FromMinutes(1)))
                {
                    Console.WriteLine(Encoding.UTF8.GetString(e.GetBytes()));
                    Console.WriteLine("----");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.ToString());
                return;
            }

            receiver.Close();
        }
```
## <a name="next-steps"></a>后续步骤

* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
