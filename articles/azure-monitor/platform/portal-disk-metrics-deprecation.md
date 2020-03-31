---
title: Azure 门户中的磁盘指标弃用 |微软文档
description: 了解已弃用哪些磁盘指标以及如何更新指标警报以使用新指标。
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: f2b960c2198800e04da77ad6b5be78d7b4762354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299798"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Azure 门户中的磁盘指标弃用

已弃用的磁盘相关指标将很快从 Azure 门户中删除。 每个弃用指标的新版本可供您使用。 本文介绍哪些指标是新的指标，以及如何更新指标警报以使用它们。

## <a name="list-of-new-metrics"></a>新指标列表

此表将每个弃用指标映射到相应的新指标。 

|已弃用的指标|新的（替换）指标|
|----|----|
|数据磁盘 QD（已弃用）|数据磁盘队列深度（预览版）|
|数据磁盘读取字节数/秒（已弃用）|数据磁盘读取字节数/秒（预览版）|
|数据磁盘读取操作数/秒（已弃用）|数据磁盘读取操作数/秒（预览版）|
|数据磁盘写入字节数/秒（已弃用）|数据磁盘写入字节数/秒（预览版）|
|数据磁盘写入操作数/秒（已弃用）|数据磁盘写入操作数/秒（预览版）|
|操作系统 QD（已弃用）|操作系统队列深度（预览）|
|操作系统读取字节/秒（已弃用）|操作系统读取字节/秒（预览）|
|操作系统读取操作/秒（已弃用）|操作系统读取操作/秒（预览）|
|操作系统写入字节/秒（已弃用）|操作系统写入字节/秒（预览）|
|操作系统写入操作/秒（已弃用）|操作系统写入操作/秒（预览）|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>迁移指标警报中的指标

更新指标警报以使用新指标。

1. 在 Azure 门户中，搜索**警报**。 然后，在 **"服务"** 部分中，选择 **"警报**"。

   > [!div class="mx-imgBorder"]
   > ![图像描述](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. 在 **"警报"** 页中，选择"**管理警报规则"** 按钮。 

   > [!div class="mx-imgBorder"]
   > ![图像描述](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. 在 **"资源组**下拉列表"中，选择 **"虚拟机**"复选框，在 **"信号类型**"下拉列表中，选择 **"指标"** 复选框。 

   > [!div class="mx-imgBorder"]
   > ![图像描述](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. 在指标列表中，标识与磁盘相关的条件。 单击规则的名称。 

   该名称在表的 **"名称"** 列中显示为超链接。

   > [!div class="mx-imgBorder"]
   > ![图像描述](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. 在 **"规则管理**"页**的条件**部分中，单击警报的条件。 

   条件显示为超链接。  

   > [!div class="mx-imgBorder"]
   > ![图像描述](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   将显示 **"配置信号逻辑**"页，并且条件的设置将显示在该页的 **"警报"逻辑**部分中。

6. 记录这些设置，因为当您删除已弃用的指标时，这些设置将消失。

   > [!div class="mx-imgBorder"]
   > ![图像描述](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > 请考虑在屏幕截图或文本文件中捕获这些设置。 

7. 单击 **"返回"到信号选择**链接。

   > [!div class="mx-imgBorder"]
   > ![图像描述](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. 在 **"配置信号逻辑**"页中，选择适当的替换指标（新指标）。 使用本文前面显示的[表](#update-metrics)来标识新指标的名称。

   > [!TIP] 
   > 开始在搜索栏中键入以缩小指标名称列表的范围。 

   > [!div class="mx-imgBorder"]
   > ![图像描述](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. 选择"**完成"** 按钮。 

   > [!div class="mx-imgBorder"]
   > ![图像描述](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. 通过选择 **"保存**"按钮提交更改。 

    > [!div class="mx-imgBorder"]
    > ![图像描述](./media/portal-disk-metrics-deprecation/save-new-metric.png)






