---
title: 向 Azure 流分析作业添加数据输入
description: 了解如何通过事件中心将数据源作为流数据挂接到流分析作业，或者通过 Blob 存储挂接引用数据。
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 713b830717cce7b4b2b0fb1171596659c2275b85
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>向流分析作业添加流数据输入或引用数据
了解如何通过事件中心将数据源作为流数据挂接到流分析作业，或者通过 Blob 存储挂接引用数据。

Azure 流分析作业可以连接到一个或多个数据输入，每个数据输入都定义了一个到现有数据源的连接。 将数据发送到数据源时，流分析作业会使用该数据并将其作为流数据进行实时处理。 流分析在作业订阅内外都与 [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)和 [Azure Blob 存储](../storage/blobs/storage-dotnet-how-to-use-blobs.md)进行高级集成。

本文是[流分析学习路径](/documentation/learning-paths/stream-analytics/)中的一个步骤。

## <a name="data-input-streaming-data-and-reference-data"></a>数据输入：流数据和引用数据
流分析中有两种不同类型的输入：数据流和引用数据。

* **数据流**：流分析作业须至少包含一种可供作业使用和转换的数据流输入。 Azure Blob 存储和 Azure 事件中心均可作为数据流输入源。 Azure 事件中心用于从连接的设备、服务和应用程序收集事件流。 Azure Blob 存储可用作按流的形式引入大量数据的输入源。  
* **引用数据**：流分析支持辅助输入的另一种类型，称为引用数据。  与运动的数据相反，该数据是静态的或变化缓慢。  它通常用于执行查找以及与数据流关联创建更丰富的数据集。  目前只支持使用 Azure Blob 存储作为引用数据的输入源。  

要向流分析作业添加输入：

1. 在 Azure 门户中，单击“输入”，并在流分析作业中单击“添加输入”。
   
    ![Azure 门户 - 添加输入。](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  
   
    在 Azure 门户中，单击流分析作业中的“输入”磁贴。  
   
    ![Azure 门户 - 添加数据输入。](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  
2. 指定输入的类型：“数据流”或“引用数据”。
   
    ![添加正确的数据输入（流式传输数据或引用数据）](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  
   
    ![添加正确的数据输入（流式传输数据或引用数据）](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  
3. 如果创建一个数据流输入，则为该输入指定源类型。  由于目前只支持 Blob 存储，在引用数据创建过程中会跳过此步骤。
   
    ![添加数据流数据输入](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  
   
    ![添加数据流数据输入](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  
4. 在输入别名框中为该输入提供一个友好的名称。  此名称以后会用于作业查询以引用该输入。
   
    填充所需连接属性的其余部分以连接到数据源。 这些字段根据输入类型和源类型而变化，[此处](stream-analytics-create-a-job.md)进行了详细定义。  
   
    ![添加事件中心数据输入](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  
5. 指定输入数据的序列化设置：
   
   * 要确保查询按期望方式进行，请指定传入数据的**事件序列化格式**。  支持的序列化格式为 JSON、CSV 和 Avro。
   * 验证数据的**编码**。  目前只支持 UTF-8 这种编码格式。
     
     ![针对数据输入的数据序列化设置](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  
     
     ![针对数据输入的数据序列化设置](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  
6. 输入创建完成后，流分析会验证是否可以连接到输入源。  可以在通知中心查看测试连接操作的状态。
   
    ![测试流式数据输入的连接](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  
   
    ![测试流式数据输入的连接](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>获取有关流数据输入的帮助
如需进一步的帮助，请尝试我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

