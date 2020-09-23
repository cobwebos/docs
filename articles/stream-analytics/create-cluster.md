---
title: 创建 Azure 流分析群集快速入门
description: 了解如何创建 Azure 流分析群集。
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: a7be204c30d242be991fb9a57d239b69342ace97
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943413"
---
# <a name="quickstart-create-a-dedicated-azure-stream-analytics-cluster-using-azure-portal"></a>快速入门：使用 Azure 门户创建 Azure 流分析专用群集

使用 Azure 门户创建 Azure 流分析群集。 [流分析群集](cluster-overview.md)是单租户部署，可用于复杂的、要求苛刻的流式处理用例。 可以在流分析群集上运行多个流分析作业。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 完成[快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)。

## <a name="create-a-stream-analytics-cluster"></a>创建流分析群集

在本节中，创建流分析群集资源。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 选择“创建资源”。  在“搜索市场”搜索框中键入并选择“流分析群集”。 然后选择“添加”。

   :::image type="content" source="./media/create-cluster/search-result.png" alt-text="流分析群集搜索结果。":::

1. 在“创建流分析群集”页上，输入新群集的基本设置。

   |设置|值|说明 |
   |---|---|---|
   |订阅|订阅名称|选择要用于此流分析群集的 Azure 订阅。 |
   |资源组|资源组名称|选择一个资源组，或者选择“新建”，然后输入新资源组的唯一名称。 |
   |群集名称|唯一的名称|输入用于标识流分析群集的名称。|
   |位置|离数据源和接收器最近的区域|选择一个地理位置来托管流分析群集。 使用离数据源和接收器最近的区域，以实现低延迟分析。|
   |流单元容量|36 至 216 |通过预估计划要运行的流分析作业数和作业需要的总 SU，确定群集的大小。 可以先设置为 36 个 SU，然后根据需要进行纵向扩展或纵向缩减。|

   ![创建群集](./media/create-cluster/create-cluster.png)

1. 选择“查看 + 创建”。 可以跳过“标记”部分。

1. 检查群集设置，然后选择“创建”。 群集创建是一个运行时间较长的操作，大约需要 60 分钟才能完成。 等待门户页显示“你的部署已完成”消息。 同时，你可以创建和开发要在此群集上运行的[流分析作业](stream-analytics-quick-create-portal.md#create-a-stream-analytics-job)（如果尚未这样做）。

1. 选择“转到资源”以转到“流分析群集”页。

## <a name="delete-your-cluster"></a>删除群集

如果你不打算在流分析群集上运行任何流分析作业，则可将该群集删除。 按照 Azure 门户上的步骤删除群集：

1. 转到“设置”下的“流分析作业”，停止所有正在运行的作业 。

1. 转到群集的“概述”。 选择“删除”，然后按照说明删除群集。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建 Azure 流分析群集。 继续学习下一篇文章，了解如何在群集上运行流分析作业：

> [!div class="nextstepaction"]
> [在流分析群集中管理流分析作业](manage-jobs-cluster.md)
