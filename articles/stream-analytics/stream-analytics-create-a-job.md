---
title: "如何为流分析创建数据分析处理作业 | Microsoft 文档"
description: "为流分析创建数据分析处理作业 | 学习路径段。"
keywords: "数据分析处理"
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: e825fbcf-69e9-443f-b402-3b7a4568f415
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 790ecf99acaf1d32a37b0f17757361b9d71778ff


---
# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>如何为流分析创建数据分析处理作业
在 Azure 流分析中的最上层资源是一个流分析作业。  它包含一个或多个输入数据源、一个表达数据转换的查询以及一个或多个结果写入的输出目标。 用户可以利用所有这些元素，针对流式数据方案进行数据分析处理。

若要开始使用流分析，请创建一个新的流分析作业。  请注意，该操作直到作业启动后才对计费产生影响。

1. 登录在线 [Azure 经典门户](http://manage.windowsazure.com)或 [Azure 门户](https://portal.azure.com/)。
2. 在门户中：单击“新建”，然后单击“数据服务”或“数据分析”（具体取决于门户），然后依次单击“Azure 流分析”或“流分析”和“快速创建”。
   
   ![数据分析处理作业向导](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  
   
   ![创建数据分析处理作业](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  
3. 指定流分析作业所需的配置。
   
   * 在“作业名称”框中，输入标识流分析作业的名称。 在验证“作业名称”后，“作业名称”框中会出现一个绿色的复选标记。 “作业名称”只能包含字母数字字符和字符“-”，且长度必须在 3 到 63 个字符之间。
   * 使用 Azure 门户中的“区域”或“位置”来指定要运行作业的地理位置。
   * 如果使用 Azure 门户，请选择或创建存储帐户以用作**区域监视存储帐户**。 该存储帐户用来存储针对该区域内运行的所有流分析作业的监控数据。
   * 如果使用 Azure 门户，请指定新的或现有**资源组**，以保存应用程序的相关资源。
4. 当新的流分析作业选项配置完成后，请单击“创建流分析作业”。 创建流分析作业需要几分钟时间。 要查看状态，你可以在通知中心监视进度。
   
   ![数据分析处理作业通知中心](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  
   
   ![Azure 门户 数据分析处理作业 创建作业](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  
5. 新作业显示的状态为“已创建”。 请注意，“启动”按钮已禁用。 你必须先配置作业输入、查询和输出，然后才能启动作业。
   
   ![数据分析处理作业作业状态](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  
   
   ![Azure 门户 数据分析处理作业 作业状态](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>获取帮助
如需进一步的帮助，请尝试我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-get-started.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Nov16_HO3-->


