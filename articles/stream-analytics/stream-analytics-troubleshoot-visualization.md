---
title: Azure 流分析作业的可视化和故障排除
description: 本文介绍如何使用诊断关系图功能直观显示流分析作业、执行自助服务故障排除。
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 523802f1f9a1dda19c5b6a66da7bc26fee851bd2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>流分析作业的可视化和故障排除
与其他基于云的技术一样，在流分析中，故障排除有时需要深入了解作业没有生成预期的输出（或者是该问题的任何输出）的原因。 考虑到这一点，流分析提供了可视化流作业的功能。 该功能作为建模工具使用起来也很方便，并且对需要其工作文档的人员来说具有附带好处。

可在可视化面板中看到输入以及正在执行的查询和配置的所有输出。 连接或配置问题变得更加明显，对配置的可视化表示形式也很有用。

## <a name="using-the-diagnosis-diagram-tool"></a>使用诊断关系图工具
要访问此可视化工具，只需单击流分析作业的“设置”区域中的“诊断关系图”按钮。

![stream-analytics-troubleshoot-visualization-diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

每个输入和输出都进行了颜色编码以表示组件的当前状态，如下所示。

![stream-analytics-troubleshoot-visualization-input-map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

当用户想要查看中间查询步骤，以了解作业内的数据流模式时，该可视化工具提供了将查询细分为其组件步骤的视图和流序列。 单击每个查询步骤会在查询编辑窗格中显示相应的部分，如图所示。 

![stream-analytics-troubleshoot-visualization-intermediate-steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

