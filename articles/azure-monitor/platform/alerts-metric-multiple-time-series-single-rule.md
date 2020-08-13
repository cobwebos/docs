---
title: 监视单个指标警报规则中的多个时序
description: 针对多个时序使用单个警报规则进行大规模警报
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 08/11/2020
ms.subservice: alerts
ms.openlocfilehash: f7ca91ca49d9357285e1307c5051ef5685ad24c9
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186891"
---
# <a name="monitor-multiple-time-series-in-a-single-metric-alert-rule"></a>监视单个指标警报规则中的多个时序

单个指标警报规则可用于监视一个或多个指标时间系列，从而更容易地大规模监视资源。

## <a name="metric-time-series"></a>指标时间系列

指标时序是一系列度量值 (或 "指标值" ) 在一段时间内捕获的。 

例如：

- 虚拟机的 CPU 使用率
- 传入字节 (入口) 到存储帐户
- Web 应用程序的失败请求数



## <a name="alert-rule-on-a-single-time-series"></a>单个时间系列上的预警规则
当警报规则满足以下所有条件时，它将监视单个时间系列：
-   规则监视单个目标资源 
-   包含单个条件
-   计算指标但不选择维度 (假设度量值支持维度) 

此类警报规则的一个示例 (仅) 显示的相关属性：
-   目标资源： *myVM1*
-   指标： *CPU 百分比*
-   运算符：*大于*
-   阈值： *70*


对于此警报规则，将监视单个指标时序：
-   *Resource*= ' myVM1 ' > 70% 的 CPU 百分比

![单个时间系列上的预警规则](media/alerts-metric-multiple-time-series-single-rule/simple-alert-rule.png)

## <a name="alert-rule-on-multiple-time-series"></a>多个时序的预警规则
如果警报规则至少使用以下功能之一，则会监视多个时间序列： 
-   多个资源
-   多个条件 
-   多个维度


## <a name="multiple-resources-multi-resource"></a>多个资源 (多资源) 

单个指标警报规则可监视多个资源，前提是资源属于同一类型并且存在于同一 Azure 区域中。 使用这种类型的规则可降低复杂性和必须维护的警报规则总数。 

此类警报规则的示例：
-   目标资源： *myVM1、myVM2*
-   指标： *CPU 百分比*
-   运算符：*大于*
-   阈值： *70*

对于此警报规则，单独监视两个指标时间系列：
-   *Resource*= ' myVM1 ' > 70% 的 CPU 百分比
-   *Resource*= ' myVM2 ' > 70% 的 CPU 百分比

![多资源警报规则](media/alerts-metric-multiple-time-series-single-rule/multi-resource-alert-rule.png)
 
在多资源警报规则中，对于每个资源) 的每个指标时序对应，**分别**为每个资源 (或更精确地评估该条件。 这意味着，还会为每个资源单独触发警报。

例如，假设我们已将上面的警报规则设置为对高于70% 的 CPU 进行监视。 在计算的时间段 (即，最后5分钟) 
-   *MyVM1*的*CPU 百分比*大于70% 
-   *MyVM2*的*CPU 百分比*为50%

警报规则在*myVM1*（而不是*myVM2*）上触发。 这些触发的警报是独立的。 它们还可以在不同的时间进行解析，具体取决于每个虚拟机的各个行为。

有关多资源警报规则以及此功能支持的资源类型的详细信息，请参阅[Azure Monitor 中的使用指标警报进行大规模监视](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)。

> [!NOTE] 
> 在监视多个资源的指标警报规则中，只允许单个条件。

## <a name="multiple-conditions-multi-condition"></a>多条件 (多条件) 

单个指标警报规则还可以监视每个警报规则最多五个条件。 

例如：

- 目标资源： *myVM1*
- Condition1
  - 指标： *CPU 百分比*
  - 运算符：*大于*
  - 阈值： *70*
- Condition2
  - 指标：*网络总数*
  - 运算符：*大于*
  - 阈值： *20 MB*

对于此警报规则，将监视两个指标时序：

- *Resource*= ' myVM1 ' > 70% 的 CPU 百分比
- 总网络，其中*Resource*= ' myVM1 ' > 20 MB

![多条件警报规则](media/alerts-metric-multiple-time-series-single-rule/multi-condition-alert-rule.png)
 
条件之间使用了 "AND" 运算符。 满足**所有**条件时，警报规则会触发警报。 如果至少有一个条件不再满足，则触发的警报会解决问题。 

> [!NOTE]
> 在包含多个条件的警报规则中使用维度时，有一些限制。 有关详细信息，请参阅在[具有多个条件的指标警报规则中使用维度时的限制](alerts-troubleshoot-metric.md#restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions)。


## <a name="multiple-dimensions-multi-dimension"></a>多维度 (多维度) 

单个指标警报规则还可以监视指标的多个维度值。 指标的维度是包含附加数据的名称-值对，用于描述指标值。 例如，存储帐户的**事务**度量值具有一个名为 " **api 名称**" 的维度，描述每个事务调用的 api 的名称 (例如，GetBlob、DeleteBlob、PutPage) 。 维度的使用是可选的，但它允许筛选度量值并仅监视特定的时间序列，而不是将度量值作为所有维度值的聚合组合在一起。 

例如，你可以选择在所有 API 名称的数量较高的情况下触发警报， (这是聚合数据) ，或者进一步将其分解为仅在事务数高为特定 API 名称时发出警报。 

监视多个维度的警报规则的一个示例是：

- 目标资源： *myStorage1*
- 度量值：*事务*
- 维度
  * API 名称 = *GetBlob、DeleteBlob、PutPage*
- 运算符：*大于*
- 阈值： *70*

对于此警报规则，将监视三个指标时序：

- *资源*= "myStorage1"、 *API 名称*= "GetBlob" > 70 的事务
- *资源*= "myStorage1"、 *API 名称*= "DeleteBlob" > 70 的事务
- *资源*= "myStorage1"、 *API 名称*= "PutPage" > 70 的事务

![包含一个维度中的值的多维度预警规则](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-1.png)

多维度指标警报规则还可以监视指标的**不同**维度中的多个维度值。 在这种情况下，预警规则将**分别**监视所选维度值的所有维度值组合。

这种类型的警报规则的一个示例：

- 目标资源： *myStorage1*
- 度量值：*事务*
- 维度
  * API 名称 = *GetBlob、DeleteBlob、PutPage*
  * Authentication = *SAS，AccountKey*
- 运算符：*大于*
- 阈值： *70*

对于此警报规则，单独监视六个指标时间系列：

- *资源*= "myStorage1"、 *API 名称*= "GetBlob" 和*Authentication*= "SAS" > 70 的事务
- *资源*= "myStorage1"、 *API 名称*= "GetBlob" 和*Authentication*= "AccountKey" > 70 的事务
- *资源*= "myStorage1"、 *API 名称*= "DeleteBlob" 和*Authentication*= "SAS" > 70 的事务
- *资源*= "myStorage1"、 *API 名称*= "DeleteBlob" 和*Authentication*= "AccountKey" > 70 的事务
- *资源*= "myStorage1"、 *API 名称*= "PutPage" 和*Authentication*= "SAS" > 70 的事务
- *资源*= "myStorage1"、 *API 名称*= "PutPage" 和*Authentication*= "AccountKey" > 70 的事务

![具有多个维度中的值的多维度预警规则](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-2.png)
 
### <a name="advanced-multi-dimension-features"></a>高级多维度功能

1.  **选择所有当前和未来的维度**-可以选择监视维度的所有可能值，包括未来值。 此类警报规则将自动缩放以监视维度的所有值，而无需在每次添加或删除维度值时修改警报规则。
2.  **排除维度**–为维度值选择 "≠" ("排除") 运算符等效于选择该维度的所有其他值，包括未来值。
3.  **新维度和自定义维度**-Azure 门户中显示的维度值基于在过去三天内收集的指标数据。 如果您要查找的维度值尚未发出，则可以添加自定义维度值。

![高级多维度功能](media/alerts-metric-multiple-time-series-single-rule/advanced-features.png)


## <a name="metric-alerts-pricing"></a>指标警报定价

指标警报规则的定价在[Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)上提供。

创建指标警报规则时，提供的价格估算基于所选功能和监视时间系列的数量，该时间是根据规则配置和当前指标值决定的。 不过，每月费用取决于时序的实际计算，因此，如果某个时间系列没有要评估的数据，或者如果警报规则使用可以使其以动态方式缩放的功能，则可能与原始估计值不同。

例如，如果某个警报规则利用了多维度功能，并且选择了大量维度值组合，则它可以显示高价格估算，这将导致监视多个时序。 但是，如果不是维度值组合实际包含要计算的数据的所有时间序列，则该警报规则的实际费用可能会降低。

## <a name="next-steps"></a>后续步骤

了解有关使用指标警报和[动态阈值](alerts-dynamic-thresholds.md)进行大规模监视的详细信息。
