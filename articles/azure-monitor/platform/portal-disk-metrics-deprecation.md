---
title: Azure 门户中的磁盘指标弃用 |Microsoft Docs
description: 了解哪些磁盘指标已弃用，以及如何更新指标警报以使用新的指标。
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: f2b960c2198800e04da77ad6b5be78d7b4762354
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299798"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Azure 门户中的磁盘指标弃用

即将从 Azure 门户中删除不推荐使用的与磁盘相关的指标。 可以使用每个不推荐使用的指标的新版本。 本文介绍了哪些指标是新的，以及如何更新指标警报以使用这些指标。

## <a name="list-of-new-metrics"></a>新度量值的列表

此表将每个不推荐使用的指标映射到它的相应新指标。 

|弃用的指标|新建（替换）指标|
|----|----|
|数据磁盘 QD （已弃用）|数据磁盘队列深度（预览）|
|数据磁盘读取字节数/秒（不推荐使用）|数据磁盘读取字节数/秒（预览版）|
|数据磁盘读取操作数/秒（不推荐使用）|数据磁盘读取操作数/秒（预览版）|
|数据磁盘写入字节数/秒（不推荐使用）|数据磁盘写入字节数/秒（预览版）|
|数据磁盘写入操作数/秒（不推荐使用）|数据磁盘写入操作数/秒（预览版）|
|OS QD （已弃用）|OS 队列深度（预览）|
|OS 读取字节数/秒（不推荐使用）|OS 读取字节数/秒（预览）|
|OS 读取操作数/秒（不推荐使用）|OS 读取操作数/秒（预览）|
|OS 写入字节数/秒（不推荐使用）|OS 写入字节数/秒（预览）|
|OS 写入操作数/秒（不推荐使用）|OS 写入操作数/秒（预览）|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>迁移指标警报中的指标

更新指标警报以使用新的指标。

1. 在 Azure 门户中，搜索 "**警报**"。 然后，在 "**服务**" 部分，选择 "**警报**"。

   > [!div class="mx-imgBorder"]
   > ![映像说明](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. 在 "**警报**" 页中，选择 "**管理警报规则**" 按钮。 

   > [!div class="mx-imgBorder"]
   > ![映像说明](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. 在 "**资源组**" 下拉列表中，选择 "**虚拟机**" 复选框，并在 "**信号类型**" 下拉列表中选择 "**指标**" 复选框。 

   > [!div class="mx-imgBorder"]
   > ![映像说明](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. 在度量值列表中，标识与磁盘相关的条件。 单击规则的名称。 

   名称在表的 "**名称**" 列中显示为超链接。

   > [!div class="mx-imgBorder"]
   > ![映像说明](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. 在 "**规则管理**" 页的 "**条件**" 部分中，单击警报的状态。 

   条件显示为超链接。  

   > [!div class="mx-imgBorder"]
   > ![映像说明](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   此时将显示 "**配置信号逻辑**" 页，并在该页面的 "**警报逻辑**" 部分显示该条件的设置。

6. 在删除不推荐使用的指标时，记录这些设置，使其消失。

   > [!div class="mx-imgBorder"]
   > ![映像说明](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > 请考虑在屏幕截图或文本文件中捕获这些设置。 

7. 单击 "**返回信号选择**" 链接。

   > [!div class="mx-imgBorder"]
   > ![映像说明](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. 在 "**配置信号逻辑**" 页上，选择适当的替换指标（新指标）。 使用本文前面部分中所示的[表](#update-metrics)标识新指标的名称。

   > [!TIP] 
   > 开始在搜索栏中键入内容，缩小度量名称列表。 

   > [!div class="mx-imgBorder"]
   > ![映像说明](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. 选择 "**完成**" 按钮。 

   > [!div class="mx-imgBorder"]
   > ![映像说明](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. 通过选择 "**保存**" 按钮提交所做的更改。 

    > [!div class="mx-imgBorder"]
    > ![映像说明](./media/portal-disk-metrics-deprecation/save-new-metric.png)






