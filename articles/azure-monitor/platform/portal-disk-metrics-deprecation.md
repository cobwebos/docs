---
title: Azure 门户中已弃用的磁盘指标 | Microsoft Docs
description: 了解哪些已弃用磁盘指标，以及如何更新指标警报以使用新指标。
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: 623d9385b9ae6b13c8964f655fb973fe67a0918a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743703"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Azure 门户中已弃用的磁盘指标

已弃用的磁盘相关指标很快就会从 Azure 门户中删除。 我们已提供每个已弃用指标的新版本供你使用。 本文介绍哪些指标是新的，以及如何更新指标警报以使用新指标。

## <a name="list-of-new-metrics"></a>新指标列表

此表列出了每个已弃用的指标及其对应的新指标。 

|已弃用的指标|新（替代）指标|
|----|----|
|数据磁盘 QD（已弃用）|数据磁盘队列深度（预览版）|
|数据磁盘读取字节数/秒（已弃用）|数据磁盘读取字节数/秒（预览版）|
|数据磁盘读取操作数/秒（已弃用）|数据磁盘读取操作数/秒（预览版）|
|数据磁盘写入字节数/秒（已弃用）|数据磁盘写入字节数/秒（预览版）|
|数据磁盘写入操作数/秒（已弃用）|数据磁盘写入操作数/秒（预览版）|
|OS QD（已弃用）|OS 队列深度（预览版）|
|OS 读取字节数/秒（已弃用）|OS 读取字节数/秒（预览版）|
|OS 读取操作数/秒（已弃用）|OS 读取操作数/秒（预览版）|
|OS 写入字节数/秒（已弃用）|OS 写入字节数/秒（预览版）|
|OS 写入操作数/秒（已弃用）|OS 写入操作数/秒（预览版）|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>迁移指标警报中的指标

更新指标警报以使用新指标。

1. 在 Azure 门户中搜索“警报”。 然后，在“服务”部分选择“警报”。 

   > [!div class="mx-imgBorder"]
   > ![警报服务](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. 在“警报”页中，选择“管理警报规则”按钮。  

   > [!div class="mx-imgBorder"]
   > ![管理警报规则](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. 在“资源组”下拉列表中选中“虚拟机”复选框，在“信号类型”下拉列表中选中“指标”复选框。    

   > [!div class="mx-imgBorder"]
   > ![筛选警报](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. 在指标列表中，识别与磁盘相关的条件。 单击规则的名称。 

   该名称将作为超链接显示在表的“名称”列中。

   > [!div class="mx-imgBorder"]
   > ![查找磁盘条件](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. 在“规则管理”页的“条件”部分，单击警报的条件。  

   条件将以超链接的形式显示。  

   > [!div class="mx-imgBorder"]
   > ![调整条件](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   此时会显示“配置信号逻辑”页，条件的设置将显示在该页的“警报逻辑”部分。 

6. 请记下这些设置，因为它们在删除已弃用的指标后会消失。

   > [!div class="mx-imgBorder"]
   > ![条件规则](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > 请考虑抓取这些设置的屏幕截图，或将其捕获到文本文件中。 

7. 单击“返回信号选择页”链接。

   > [!div class="mx-imgBorder"]
   > ![返回到信号选择](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. 在“配置信号逻辑”页上，选择适当的替代指标（新指标）。 使用本文前面提供的[表格](#update-metrics)来识别新指标的名称。

   > [!TIP] 
   > 开始在搜索栏中键入内容，以缩小指标名称列表的搜索范围。 

   > [!div class="mx-imgBorder"]
   > ![选择新度量值](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. 选择“完成”按钮。 

   > [!div class="mx-imgBorder"]
   > ![设置新指标](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. 选择“保存”按钮提交更改。 

    > [!div class="mx-imgBorder"]
    > ![保存新指标](./media/portal-disk-metrics-deprecation/save-new-metric.png)






