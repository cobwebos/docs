---
title: 管理指标顾问中的数据馈送
titleSuffix: Azure Cognitive Services
description: 了解如何管理已添加到指标顾问的数据馈送。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: mbullwin
ms.openlocfilehash: de8d5d8e9fc5ce1df05cfd4c67ef146760e2c7e9
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043164"
---
# <a name="how-to-manage-your-data-feeds"></a>如何：管理数据馈送

了解如何在指标顾问中管理载入数据馈送。 本文将指导你完成在指标监视器中管理数据馈送的步骤。

## <a name="edit-a-data-feed"></a>编辑数据馈送

> [!NOTE]
> 创建数据馈送后，无法更改以下详细信息。 
> * 数据馈送 ID
> * 创建时间
> * 维度
> * 源类型
> * 粒度

仅允许数据馈送的管理员对其进行更改。 

暂停或重新激活数据馈送：

1. 在 "数据馈送列表" 页上，单击您要对数据源执行的操作。

2. 在 "数据馈送详细信息" 页上，单击 " **状态** " 切换按钮。

删除数据馈送： 

1. 在 "数据馈送列表" 页上，单击 "数据馈送" 上的 " **删除** "。

2. 在 "数据馈送详细信息" 页上，单击 " **删除**"。

更改开始时间时，需要再次验证该架构。 可以通过使用 **编辑参数**来更改它。

##  <a name="backfill-your-data-feed"></a>回填数据馈送

选择 "  **回填** " 按钮可在时间戳上触发立即引入，以修复失败的引入或覆盖现有数据。
- 开始时间为包含时间。
- 结束时间为排他。
- 异常检测仅在所选范围内重新触发。

:::image type="content" source="../media/datafeeds/backfill-datafeed.png" alt-text="回填数据馈送":::

## <a name="manage-permission-of-a-data-feed"></a>管理数据馈送的权限

工作区访问权限由指标顾问资源控制，该资源使用 Azure Active Directory 进行身份验证。 向指标数据应用另一层权限控制。

利用指标顾问，您可以向不同的人员组授予不同数据馈送的权限。 有两种类型的角色： 

- 管理员：拥有管理数据馈送的完全权限的管理员，包括修改和删除。
- 查看器：有权访问数据馈送的只读视图。
 

## <a name="advanced-settings"></a>高级设置

创建新的数据馈送时有几个可选的高级设置，可以在数据馈送详细信息页中修改这些设置。

### <a name="ingestion-options"></a>摄取选项

* **引入时间偏移**：默认情况下，根据指定的粒度对数据进行引入。 例如，具有 *每日* 时间戳的度量值将在其时间戳之后引入。 您可以使用偏移量来延迟引入的时间，使用 *正数* ，或使用 *负数* 来提前。

* **Max concurrency**：如果数据源支持有限并发，请设置此参数。 否则，请保留默认设置。

* **在以下时间后停止重试**：如果数据引入失败，它将在一段时间内自动重试。 期初是第一次数据引入的时间。 期间的长度根据粒度定义。 如果保留默认值 (-1) ，则将根据下面的粒度确定该值。
    
    | 粒度       | 停止重试           |
    | :------------ | :--------------- |
    | 每日，自定义 ( # B0 = 1 天) 、每周、每月、每年     | 7 天          |
    | 每小时，自定义 ( # A0 1 天)        | 72小时 |

* **最小重试间隔**：可以指定重试从源中提取数据时的最小间隔。 如果保留默认值 (-1) ，则将根据下面的粒度确定重试间隔。
    
    | 粒度       | 最小重试间隔           |
    | :------------ | :--------------- |
    | 每日，自定义 ( # B0 = 1 天) ，每周，每月     | 30 分钟          |
    | 每小时，自定义 ( # A0 1 天)       | 10 分钟 |
    | 每年 | 1 天          |
 
### <a name="fill-gap-when-detecting"></a>检测时填充间隙： 

> [!NOTE]
> 此设置不会影响数据源，也不会影响门户上显示的数据图表。 仅在异常检测期间进行自动填充。

某些时序不是连续的。 当缺少数据点时，指标顾问将使用指定的值在异常检测之前填充它们以提高准确性。
选项包括： 

* 使用以前的实际数据点的值。 默认情况下使用此值。
* 使用特定值。

### <a name="action-link-template"></a>操作链接模板： 

操作链接模板用于预定义可操作的 HTTP url，其中包含占位符、、、 `%datafeed` `%metric` `%timestamp` `%detect_config` 和 `%tagset` 。 您可以使用模板从异常或事件重定向到特定 URL 以向下钻取。

:::image type="content" source="../media/action-link-template.png" alt-text="回填数据馈送" lightbox="../media/action-link-template.png":::

填写 "操作" 链接后，请单击事件列表的 "操作" 选项上的 " **前往操作" 链接** ，然后单击事件树的右键单击菜单。 将操作链接模板中的占位符替换为异常或事件的相应值。

| 占位符 | 示例 | 评论 |
| ---------- | -------- | ------- |
| `%datafeed` | - | 数据馈送 ID |
| `%metric` | - | 指标 ID |
| `%detect_config` | - | 检测配置 ID |
| `%timestamp` | - | 持久事件的异常或结束时间的时间戳 |
| `%tagset` | `%tagset`, <br> `[%tagset.get("Dim1")]`, <br> `[ %tagset.get("Dim1", "filterVal")]` | 事件的异常或异常情况的维度值。   <br> `filterVal`用于筛选方括号内的匹配值。   |

示例

* 如果操作链接模板为 `https://action-link/metric/%metric?detectConfigId=%detect_config` ，
  * 操作链接 `https://action-link/metric/1234?detectConfigId=2345` 将跳至 "指标" 下的异常或事件 `1234` 并检测配置 `2345` 。

* 如果操作链接模板为 `https://action-link?[Dim1=%tagset.get('Dim1','')&][Dim2=%tagset.get('Dim2','')]` ， 
    * `https://action-link?Dim1=Val1&Dim2=Val2`当异常为时，操作链接会是 `{ "Dim1": "Val1", "Dim2": "Val2" }` 。 
    * `https://action-link?Dim2=Val2`当异常为时，操作链接将为 `{ "Dim1": "", "Dim2": "Val2" } ` ，因为 `[Dim1=***&]` 维度值为空字符串，将跳过该操作。 

* 如果操作链接模板为 `https://action-link?filter=[Name/Dim1 eq '%tagset.get('Dim1','')' and ][Name/Dim2 eq '%tagset.get('Dim2','')']` ， 
    * `https://action-link?filter=Name/Dim1 eq 'Val1' and Name/Dim2 eq 'Val2'`当异常为时，操作链接会是 `{ "Dim1": "Val1", "Dim2": "Val2" }` ， 
    * `https://action-link?filter=Name/Dim2 eq 'Val2'`如果 `{ "Dim1": "", "Dim2": "Val2" }` `[Name/Dim1 eq '***' and ]` 因维度值为空字符串而跳过了异常，则操作链接将为。 
   
### <a name="data-feed-not-available-alert-settings"></a>"数据源不可用" 警报设置

如果在数据馈送开始引入时指定的宽限期内未引入数据，则会将数据馈送视为不可用。 在这种情况下，会触发警报。

若要配置警报，需要先 [创建一个挂钩](alerts.md#create-a-hook) 。 将通过配置的挂钩发送警报。

* **宽限期**： "宽限期" 设置用于确定当没有数据点引入时发送警报的时间。 参考点是第一次引入的时间。 如果引入失败，指标顾问将继续尝试由粒度指定的固定间隔。 如果在宽限期内仍出现故障，则会发送警报。

* **自动暂停**：将此选项设置为 "零" 时，每个 " *不可* 用" 时间戳都将触发警报。 如果指定的设置不是零，则不会根据指定的设置触发 *不* 能使用的第一个时间戳。

## <a name="next-steps"></a>后续步骤
- [配置指标并微调检测配置](configure-metrics.md)
- [使用反馈调整异常检测](anomaly-feedback.md)
- [诊断事件](diagnose-incident.md)。
