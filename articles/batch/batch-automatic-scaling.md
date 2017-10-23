---
title: "自动缩放 Azure Batch 池中的计算节点 | Microsoft Docs"
description: "对云池启用自动缩放功能可以动态调整池中计算节点的数目。"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: multiple
ms.date: 06/20/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0e49cd8a64a48c53f5b6104703164a597c797f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>创建用于缩放 Batch 池中的计算节点的自动缩放公式

Azure Batch 可以根据定义的参数自动缩放池。 通过自动缩放，Batch 在任务需求提高时动态将节点添加到池中，并在任务需求降低时删除计算节点。 可以通过自动调整 Batch 应用程序使用的计算节点数来节省时间和资金。 

可以通过将计算节点池与你定义的自动缩放公式关联，来启用该池的自动缩放。 Batch 服务会使用自动缩放公式确定执行工作负荷所需的计算节点数。 计算节点可以是专用节点，也可以是[低优先级节点](batch-low-pri-vms.md)。 Batch 会响应定期收集的服务指标数据。 Batch 使用此指标数据，基于公式并按可配置的间隔来调整池中的计算节点数。

可以在创建池时启用自动缩放，也可以对现有池启用该功能。 还可以更改已配置自动缩放的池的现有公式。 Batch 使你可以在将公式分配给池之前先评估公式，以及监视自动缩放运行的状态。

本文讨论构成自动缩放公式的各个实体，包括变量、运算符、操作和函数。 本文介绍如何在 Batch 中获取各种计算资源和任务指标。 可以使用这些指标，根据资源使用情况和任务状态对池的节点计数进行调整。 然后，介绍如何使用 Batch REST 和 .NET API 构建公式以及对池启用自动缩放。 最后，讨论几个示例公式。

> [!IMPORTANT]
> 创建 Batch 帐户时，可以指定[帐户配置](batch-api-basics.md#account)，用于确定是要在 Batch 服务订阅（默认设置）还是用户订阅中分配池。 如果使用默认的 Batch 服务配置创建了 Batch 帐户，则该帐户将限制为可用于处理的最大核心数。 Batch 服务最多只能将计算节点数扩展到该核心数限制。 出于此原因，Batch 服务可能达不到自动缩放公式所指定的目标计算节点数。 请参阅 [Azure Batch 服务的配额和限制](batch-quota-limit.md)了解有关查看和提高帐户配额的信息。
>
>如果使用用户订阅配置创建了帐户，则该帐户将共享订阅的核心配额。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额和约束条件](../azure-subscription-service-limits.md)中的[虚拟机限制](../azure-subscription-service-limits.md#virtual-machines-limits)。
>
>

## <a name="automatic-scaling-formulas"></a>自动缩放公式
自动缩放公式是一个定义的包含一个或多个语句的字符串值。 自动缩放公式会分配给池的 [autoScaleFormula][rest_autoscaleformula] 元素 (Batch REST) 或 [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] 属性 (Batch .NET)。 Batch 服务使用公式来确定池中可供下一个处理间隔使用的目标计算节点数。 公式字符串不能超过 8 KB，最多可以包含 100 个以分号分隔的语句，可以包括换行符和注释。

可以将自动缩放公式视为 Batch 自动缩放“语言”。 公式语句是自由形式的表达式，可以包括服务定义的变量（由 Batch 服务定义的变量）和用户定义的变量（定义的变量）。 公式语句可以通过内置类型、运算符和函数对这些值执行各种操作。 例如，语句可以采用以下格式：

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

公式通常包含多个语句，这些语句对先前语句中获取的值执行操作。 例如，首先获取 `variable1` 的值，然后将其传递给一个函数来填充 `variable2`：

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

在自动缩放公式中包含这些语句可实现计算节点的目标数。 专用节点和低优先级节点都有自身的目标设置，因此，你可以针对每种类型的节点定义目标。 自动缩放公式可以包含专用节点的目标值和/或低优先级节点的目标值。

节点的目标数可以大于、小于或等于池中该节点类型的当前数目。 Batch 按特定的间隔（请参阅[自动缩放间隔](#automatic-scaling-interval)）对池的自动缩放公式求值。 Batch 将池中每种节点类型的目标数调整成在求值时自动缩放公式所指定的数目。

### <a name="sample-autoscale-formula"></a>示例自动缩放公式

下面是可以进行调整以适用于大多数方案的自动缩放公式示例。 示例公式中的变量 `startingNumberOfVMs` 和 `maxNumberofVMs` 可以根据需要进行调整。 此公式缩放专用节点，但可对其进行修改，使其也适用于缩放低优先级节点。 

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

对于此自动缩放公式，最初使用单个 VM 创建池。 `$PendingTasks` 指标定义正在运行或已排队的任务数。 该公式查找过去 180 秒内的平均挂起任务数，并相应地设置 `$TargetDedicatedNodes` 变量。 该公式确保专用节点的目标数永远不会超过 25 个 VM。 提交新任务时，池会自动增大。 任务完成时，VM 会逐个变为可用状态，自动缩放公式会收缩池。

## <a name="variables"></a>变量
可在自动缩放公式中同时使用“服务定义”和“用户定义”的变量。 服务定义的变量内置在 Batch 服务中。 有些服务定义的变量是可读写的，有些是只读的。 用户定义的变量是你定义的变量。 在上一节中所示的示例公式中，`$TargetDedicatedNodes` 和 `$PendingTasks` 是服务定义的变量。 变量 `startingNumberOfVMs` 和 `maxNumberofVMs` 是用户定义的变量。

> [!NOTE]
> 服务定义的变量始终前面带有美元符号 ($)。 对于用户定义的变量，美元符号是可选的。
>
>

下表显示了 Batch 服务定义的读写和只读变量。

可以获取和设置这些服务定义的变量的值，以管理池中计算节点的数目：

| 可读写的服务定义变量 | 说明 |
| --- | --- |
| $TargetDedicatedNodes |池的专用计算节点的目标数。 专用节点数指定为目标，因为池可能永远达不到所需的节点数目。 例如，如果在池达到初始目标数之前专用节点的目标数被自动缩放评估修改，则池可能不会达到目标数数。 <br /><br /> 如果目标数超过了 Batch 帐户节点或核心配额，则使用 Batch 服务配置创建的帐户中的池无法实现其目标。 如果目标数超过了订阅的共享核心配额，则使用用户订阅配置创建的帐户中的池无法实现其目标。|
| $TargetLowPriorityNodes |池的低先级计算节点的目标数。 低优先级节点数指定为目标，因为池可能永远达不到所需的节点数目。 例如，如果在池达到初始目标数之前低优先级的目标数被自动缩放评估修改，则池可能不会达到目标数数。 如果目标数超过 Batch 帐户节点或核心配额，则池也无法实现其目标。 <br /><br /> 有关低优先级计算节点的详细信息，请参阅[使用 Batch 中的低优先级 VM（预览版）](batch-low-pri-vms.md)。 |
| $NodeDeallocationOption |从池中删除计算节点时发生的操作。 可能的值包括：<ul><li>**requeue**--立即终止任务并将其放回作业队列，以便重新计划这些任务。<li>**terminate**--立即终止任务并将其从作业队列中删除。<li>**taskcompletion**--等待当前运行的任务完成，并从池中删除节点。<li>**retaineddata**--等待清理节点上的本地任务保留的所有数据，并从池中删除节点。</ul> |

可以获取这些服务定义的变量的值，以根据 Batch 服务中的指标进行调整：

| 只读的服务定义变量 | 说明 |
| --- | --- |
| $CPUPercent |CPU 使用率的平均百分比。 |
| $WallClockSeconds |使用的秒数。 |
| $MemoryBytes |使用的平均 MB 数。 |
| $DiskBytes |本地磁盘上使用的平均 GB 数。 |
| $DiskReadBytes |读取的字节数。 |
| $DiskWriteBytes |写入的字节数。 |
| $DiskReadOps |执行的读取磁盘操作数。 |
| $DiskWriteOps |执行的写入磁盘操作数。 |
| $NetworkInBytes |入站字节数。 |
| $NetworkInBytes |出站字节数。 |
| $SampleNodeCount |计算节点数。 |
| $ActiveTasks |已准备好执行但尚未执行的任务数。 $ActiveTasks 计数包括处于活动状态并且已满足其依赖关系的所有任务。 处于活动状态但不满足其依赖关系的所有任务将从 $ActiveTasks 计数中排除。|
| $RunningTasks |处于运行状态的任务数。 |
| $PendingTasks |$ActiveTasks 和 $RunningTasks 的总和。 |
| $SucceededTasks |成功完成的任务数。 |
| $FailedTasks |失败的任务数。 |
| $CurrentDedicatedNodes |当前的专用计算节点数。 |
| $CurrentLowPriorityNodes |当前的低优先级计算节点数，包括所有已预先清空的节点。 |
| $PreemptedNodeCount | 池中处于预先清空状态的节点数。 |

> [!TIP]
> 上表中所示的服务定义的只读变量是一些对象，它们提供各种方法来访问与其相关的数据。 有关详细信息，请参阅本文稍后的[获取样本数据](#getsampledata)。
>
>

## <a name="types"></a>类型
公式支持以下类型：

* double
* doubleVec
* doubleVecList
* 字符串
* timestamp--timestamp 是包含以下成员的复合结构：

  * year
  * month (1-12)
  * day (1-31)
  * weekday（采用数字格式，例如 1 表示星期一）
  * hour（采用 24 时制数字格式，例如 13 表示下午 1 点）
  * minute (00-59)
  * second (00-59)
* timeinterval

  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>操作
上一部分中列出的类型允许以下操作。

| 操作 | 支持的运算符 | 结果类型 |
| --- | --- | --- |
| double *operator* double |+, -, *, / |double |
| double *operator* timeinterval |* |timeinterval |
| doubleVec *operator* double |+, -, *, / |doubleVec |
| doubleVec *operator* doubleVec |+, -, *, / |doubleVec |
| timeinterval *operator* double |*, / |timeinterval |
| timeinterval *operator* timeinterval |+, - |timeinterval |
| timeinterval *operator* timestamp |+ |timestamp |
| timestamp *operator* timeinterval |+ |timestamp |
| timestamp *operator* timestamp |- |timeinterval |
| *operator*double |-, ! |double |
| *operator*timeinterval |- |timeinterval |
| double *operator* double |<, <=, ==, >=, >, != |double |
| string *operator* string |<, <=, ==, >=, >, != |double |
| timestamp *operator* timestamp |<, <=, ==, >=, >, != |double |
| timeinterval *operator* timeinterval |<, <=, ==, >=, >, != |double |
| double *operator* double |&&, &#124;&#124; |double |

使用三元运算符 (`double ? statement1 : statement2`) 测试双精度值时，非零值为 **true**，零值为 **false**。

## <a name="functions"></a>函数
可以使用以下预定义**函数**来定义自动缩放公式。

| 函数 | 返回类型 | 说明 |
| --- | --- | --- |
| avg(doubleVecList) |double |返回 DoubleVecList 中所有值的平均值。 |
| len(doubleVecList) |double |返回从 doubleVecList 创建的矢量的长度。 |
| lg(double) |double |返回 double 的对数底数 2。 |
| lg(doubleVecList) |doubleVec |返回 doubleVecList 的分量对数底数 2。 必须为参数显式传递 vec(double)。 否则会采用 double lg(double) 版本。 |
| ln(double) |double |返回 double 的自然对数。 |
| ln(doubleVecList) |doubleVec |返回 doubleVecList 的分量对数底数 2。 必须为参数显式传递 vec(double)。 否则会采用 double lg(double) 版本。 |
| log(double) |double |返回 double 的对数底数 10。 |
| log(doubleVecList) |doubleVec |返回 doubleVecList 的分量对数底数 10。 对于单一的 double 参数，必须显式传递 vec(double)。 否则会采用 double log(double) 版本。 |
| max(doubleVecList) |double |返回 doubleVecList 中的最大值。 |
| min(doubleVecList) |double |返回 doubleVecList 中的最小值。 |
| norm(doubleVecList) |double |返回从 doubleVecList 创建的矢量的二范数。 |
| percentile(doubleVec v, double p) |double |返回矢量 v 的百分位元素。 |
| rand() |double |返回介于 0.0 和 1.0 之间的随机值。 |
| range(doubleVecList) |double |返回 doubleVecList 中最小值和最大值之间的差。 |
| std(doubleVecList) |double |返回 doubleVecList 中值的样本标准偏差。 |
| stop() | |停止对自动缩放表达式求值。 |
| sum(doubleVecList) |double |返回 doubleVecList 的所有组成部分之和。 |
| time(string dateTime="") |timestamp |如果未传递参数，则返回当前时间的时间戳；如果传递了参数，则返回 dateTime 字符串的时间戳。 支持的 dateTime 格式为 W3C-DTF 和 RFC 1123。 |
| val(doubleVec v, double i) |double |返回在起始索引为零的矢量 v 中，位置 i 处的元素的值。 |

上表中描述的某些函数可以接受列表作为参数。 逗号分隔列表为 *double* 和 *doubleVec* 的任意组合。 例如：

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

doubleVecList 值在计算之前将转换为单个 doubleVec。 例如，如果 `v = [1,2,3]`，则调用 `avg(v)` 相当于调用 `avg(1,2,3)`。 调用 `avg(v, 7)` 相当于调用 `avg(1,2,3,7)`。

## <a name="getsampledata"></a>获取样本数据
自动缩放公式使用 Batch 服务提供的指标数据（样本）。 公式根据服务所提供的值来扩大或缩小池的大小。 上述服务定义的变量是可提供各种方法来访问与该对象关联的数据的对象。 例如，以下表达式显示了一个用于获取过去五分钟 CPU 使用率的请求：

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| 方法 | 说明 |
| --- | --- |
| GetSample() |`GetSample()` 方法返回数据样本的矢量。<br/><br/>一个样本最好包含 30 秒钟的指标数据。 换而言之，将每隔 30 秒获取一次样本。 但如下所示，样本在收集后需经历一定的延迟才能供公式使用。 因此，并非一段指定时间内的所有样本都可用于公式求值。<ul><li>`doubleVec GetSample(double count)`<br/>指定从已收集的最近样本中获得的样本数。<br/><br/>`GetSample(1)` 返回最后一个可用样本。 但对于像 `$CPUPercent` 这样的度量值，不应使用此方法，因为无法知道样本是何时收集的。 它可能是最近收集的，也可能由于系统问题而变得很旧。 最好使用如下所示的时间间隔。<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>指定收集样本数据的时间范围。 （可选）它还指定必须在请求的时间范围内提供的样本的百分比。<br/><br/>如果 CPUPercent 历史记录中存在过去 10 分钟的所有样本，`$CPUPercent.GetSample(TimeInterval_Minute * 10)` 将返回 20 个样本。 但如果最后一分钟的历史记录不可用，则只返回 18 个样本。 在这种情况下：<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` 会失败，因为仅 90% 的样本可用。<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` 将成功。<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>指定收集数据的时间范围（包括开始时间和结束时间）。<br/><br/>如前所述，每收集一个样本后并且该样本可供公式使用时，会存在一定的延迟。 使用 `GetSample` 方法时，请考虑到这种延迟。 请参阅下面的 `GetSamplePercent`。 |
| GetSamplePeriod() |返回在历史样本数据集中采样的期间。 |
| Count() |返回指标历史记录中的样本总数。 |
| HistoryBeginTime() |返回指标最旧可用数据样本的时间戳。 |
| GetSamplePercent() |返回给定时间间隔的可用样本百分比。 例如：<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>由于 `GetSample` 方法失败，因此如果返回的样本百分比低于指定的 `samplePercent`，则可先使用 `GetSamplePercent` 方法进行检查。 然后，如果存在的样本数量不足，可以执行其他操作，无需停止自动缩放评估。 |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>样本、样本百分比和 *GetSample()* 方法
自动缩放公式的核心操作是获取任务和资源指标数据，然后根据该数据调整池大小。 因此，请务必明确知道自动缩放公式如何与指标数据（样本）交互。

**示例**

Batch 服务定期获取任务和资源指标的样本，使其可供自动缩放公式使用。 这些样本每 30 秒由 Batch 服务记录一次。 但是，记录样本的时间与样本可供自动缩放公式使用（与读取）的时间之间通常存在延迟。 此外，由于各种因素（例如网络或其他基础结构问题），可能无法记录特定间隔的样本。

**样本百分比**

将 `samplePercent` 传递到 `GetSample()` 方法，或调用 `GetSamplePercent()` 方法时，“百分比”是指 Batch 服务记录的样本可能的总数与自动缩放公式可用的样本数之间的比值。

让我们以 10 分钟的时间跨度为例。 由于每隔 30 秒记录样本一次，因此在 10 分钟的时间跨度内，Batch 服务所记录的样本总数将达到 20 个（每分钟 2 个）。 但是，由于报告机制固有的延迟，并且 Azure 中存在其他问题，可能只有 15 个样本可供自动缩放公式读取。 因此，举例来说，在这 10 分钟内，记录的样本总数只有 75% 可供公式使用。

**GetSample() 和样本范围**

自动缩放公式将对池进行扩大和缩小操作 &mdash; 添加节点或删除节点。 由于节点耗费资金，需要确保公式所使用的智能分析方法采用了足够的数据。 因此，建议在公式中使用趋势类型的分析。 此类型会根据所收集样本的范围来扩大和缩小池。

为此，请使用 `GetSample(interval look-back start, interval look-back end)` 返回样本的矢量：

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Batch 评估上述代码行后，会以值的矢量形式返回样本范围。 例如：

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

收集样本矢量后，便可使用 `min()`、`max()` 和 `avg()` 等函数从所收集的范围派生有意义的值。

为了提高安全性，如果特定时间段小于特定的样本百分比，可强制公式求值失败。 强制公式求值失败会指示 Batch 在无法提供指定百分比的样本数时停止进一步的公式求值。 在这种情况下，不更改池大小。 要指定求值成功所需的样本百分比，请将其指定为 `GetSample()` 的第三个参数。 下面指定要求 75% 的样本：

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

由于样本可用性可能存在延迟，因此请务必始终指定回查开始时间早于一分钟的时间范围。 样本需要花大约一分钟的时间才能传播到整个系统，因此可能无法使用 `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` 范围内的样本。 同样地，可以使用 `GetSample()` 百分比参数来强制实施特定样本百分比要求。

> [!IMPORTANT]
> **强烈建议****不要仅依赖自动缩放公式中的 `GetSample(1)`**。 这是因为，`GetSample(1)` 基本上只是向 Batch 服务表明：“不论多久以前检索最后一个样本，请将它提供给我。” 由于它只是单个样本，而且可能是较旧的样本，因此可能无法代表最近任务或资源状态的全貌。 如果使用 `GetSample(1)`，请确保它是更大语句的一部分，而不是公式所依赖的唯一数据点。
>
>

## <a name="metrics"></a>指标
在定义公式时，可以同时使用资源和任务指标。 可根据获取和求值的指标数据对池中专用节点的目标数进行调整。 有关每个指标的详细信息，请参见上面的[变量](#variables)部分。

<table>
  <tr>
    <th>指标</th>
    <th>说明</th>
  </tr>
  <tr>
    <td><b>资源</b></td>
    <td><p>资源指标基于计算节点的 CPU、带宽和内存使用量以及节点数。</p>
        <p> 这些服务定义的变量可用于根据节点计数进行调整：</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$preemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>这些服务定义的变量可用于根据节点资源使用量进行调整：</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkInBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>任务</b></td>
    <td><p>任务指标基于任务的状态（例如活动、挂起和已完成）。 以下服务定义的变量可用于根据任务指标调整池大小：</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>编写自动缩放公式
构建自动缩放公式时，可以使用上述组件来生成语句，然后将这些语句组合成完整的公式即可。 本部分将创建一个示例自动缩放公式，它可以执行一些实际缩放决策。

首先，定义新自动缩放公式的要求。 该公式应：

1. 如果 CPU 使用率高，则增加池中专用计算节点的目标数。
2. 如果 CPU 使用率低，则减少池中专用计算节点的目标数。
3. 始终将最大专用节点数限制为 400。

若要在 CPU 使用率高时增加节点数，可定义一个语句，仅当过去 10 分钟内的最小平均 CPU 使用率高于 70% 时，该语句才会向用户定义变量 (`$totalDedicatedNodes`) 填充一个值，值的大小为专用节点当前目标数的 110%。 否则，使用当前专用节点数的值。

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

为了在 CPU 使用率低时减少专用节点数，如果过去 60 分钟的平均 CPU 使用率低于 20%，则公式中的下一个语句会将同一 `$totalDedicatedNodes` 变量设置为专用节点当前目标数的 90%。 否则，使用在以上语句中填充的 `$totalDedicatedNodes` 的当前值。

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

现在，将专用计算节点的目标数限制为最大值 400：

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

下面是完整公式：

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-net"></a>使用 .NET 创建启用自动缩放的池

若要在 .NET 中创建启用自动缩放的池，请遵循以下步骤：

1. 使用 [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool) 创建池。
2. 将 [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) 属性设置为 `true`。
3. 使用自动缩放公式设置 [ CloudPool.AutoScaleFormula ](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) 属性。
4. （可选）设置 [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) 属性（默认值为 15 分钟）。
5. 使用 [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) 或 [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync) 提交池。

以下代码片段在 .NET 中创建启用自动缩放的池。 该池的自动缩放公式在星期一将专用节点的目标数设置为 5，在其他星期日期将该目标数设置为 1。 [自动缩放间隔](#automatic-scaling-interval)设置为 30 分钟。 在本文的此部分与其他 C# 代码片段中，`myBatchClient` 是 [BatchClient][net_batchclient] 类的适当初始化的实例。

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "small", // single-core, 1.75 GB memory, 225 GB disk
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> 创建启用自动缩放的池时，请不要在 **CreatePool** 调用中指定 _targetDedicatedComputeNodes_ 参数或 _targetLowPriorityComputeNodes_ 参数。 应该指定池中的 **AutoScaleEnabled** 和**AutoScaleFormula** 属性。 这些属性的值确定每种类型的节点的目标数。 另请注意，若要手动调整启用自动缩放功能的池的大小（例如，使用 [BatchClient.PoolOperations.ResizePoolAsync][net_poolops_resizepoolasync] 来调整），则必须先**禁用**该池的自动缩放，然后调整其大小。
>
>

除了 Batch .NET 以外，还可使用其他任何 [Batch SDK](batch-apis-tools.md#azure-accounts-for-batch-development)、[Batch REST](https://docs.microsoft.com/rest/api/batchservice/)、[Batch PowerShell cmdlets](batch-powershell-cmdlets-get-started.md) 和 [Batch CLI](batch-cli-get-started.md) 来配置自动缩放。


### <a name="automatic-scaling-interval"></a>自动缩放间隔
默认情况下，Batch 服务根据其自动缩放公式每隔 15 分钟调整池大小。 可使用以下池属性配置此间隔：

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (REST API)

最小间隔为 5 分钟，最大间隔为 168 小时。 如果指定的间隔超出此范围，Batch 服务将返回“错误的请求(400)”错误。

> [!NOTE]
> 自动缩放目前不能以低于一分钟的时间响应更改，而只能在运行工作负荷时逐步调整池大小。
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>启用现有池的自动缩放

每个 Batch SDK 都提供了启用自动缩放的方式。 例如：

* [BatchClient.PoolOperations.EnableAutoScaleAsync][net_enableautoscaleasync] (Batch .NET)
* [对池启用自动缩放][rest_enableautoscale] (REST API)

启用现有池的自动缩放时，请注意以下要点：

* 发出启用自动缩放的请求时，如果池中的自动缩放已禁用，则必须在发出请求时指定有效的自动缩放公式。 可以选择性地指定自动缩放评估间隔。 如果不指定间隔，则使用默认值 15 分钟。
* 如果池中的自动缩放目前已启用，则可指定自动缩放公式和/或评估间隔。 必须至少指定其中的一个属性。

  * 如果指定新的自动缩放评估间隔，将停止现有评估计划并开始新的计划。 新计划的开始时间是发出启用自动缩放的请求的时间。
  * 如果省略自动缩放公式或评估间隔，则 Batch 服务将继续使用该设置的当前值。

> [!NOTE]
> 如果在 .NET 中创建池时指定了 **CreatePool** 方法的 *targetDedicatedComputeNodes* 或 *targetLowPriorityComputeNodes* 参数的值，或者在其他语言中指定了相应参数的值，则评估自动缩放公式时将忽略这些值。
>
>

此 C# 代码片段使用 [Batch.NET][net_api] 库启用现有池的自动缩放：

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>更新自动缩放公式

若要更新现有的已启用自动缩放的池的公式，请使用新公式再次调用启用自动缩放的操作。 例如，如果在执行以下 .NET 代码时已在 `myexistingpool` 上启用自动缩放，则自动缩放公式将替换为 `myNewFormula` 的内容。

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>更新自动缩放间隔

若要更新现有的已启用自动缩放的池的自动缩放评估间隔，请使用新间隔再次调用启用自动缩放的操作。 例如，将 .NET 中已启用自动缩放的池的自动缩放评估间隔设置为 60 分钟：

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>评估自动缩放公式

可以在将公式应用于池之前对其进行评估。 这样，便可以测试公式，以便在将公式放入生产之前查看其语句的评估方式。

若要评估自动缩放公式，必须先通过有效的公式对池启用自动缩放。 若要在尚未启用自动缩放的池上测试公式，请在首次启用自动缩放时使用单行公式 `$TargetDedicatedNodes = 0`。 然后使用以下方法之一来评估要测试的公式：

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) 或 [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    这些 Batch.NET 方法需要现有池的 ID 和包含要评估的自动缩放公式的字符串。

* [评估自动缩放公式](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    在此 REST API 请求中，在 URI 中指定池 ID，并在请求正文的 autoScaleFormula 元素中指定自动缩放公式。 操作的响应包含任何可能与该公式相关的错误信息。

我们将在此 [Batch.NET][net_api] 代码片段中评估自动缩放公式。 如果池未启用自动缩放，先启用自动缩放。

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = {pool.CurrentDedicatedNodes};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

如果此代码片段中所示的公式评估成功，将生成如下所示的结果：

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>获取有关自动缩放运行的信息

为确保公式按预期执行，建议定期检查 Batch 在池上执行的自动缩放运行的结果。 为此，获取（或刷新）对池的引用，并检查上一次自动缩放运行的属性。

在 Batch .NET 中，[CloudPool.AutoScaleRun ](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) 属性具有多个属性，其提供了有关在池上执行的最新自动缩放运行的信息：

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

在 REST API 中，[获取有关池的信息](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool)请求返回有关池的信息，其中包括 [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool#bk_autrun) 属性中最新自动缩放运行的信息。

以下 C# 代码片段使用 Batch .NET 库来打印有关池 _myPool_ 上的最新自动缩放运行的信息：

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

以上代码段的示例输出：

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>示例自动缩放公式
让我们查看一些公式，它们演示了调整池中计算资源数量的不同方法。

### <a name="example-1-time-based-adjustment"></a>示例 1：基于时间的调整
假设你想要根据星期日期和当天的时间来调整池的大小。 此示例演示如何相应地增加或减少池中的节点数。

该公式首先获取当前时间。 如果日期是工作日（周一到周五）且时间是工作时间（上午 8 点到下午 6 点），则会将目标池大小设置为 20 个节点。 否则，它将设置为 10 个节点。

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>示例 2：基于任务的调整
在此示例中，池大小是根据队列中的任务数来调整的。 在公式字符串中，注释和分行符都是可以接受的。

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>示例 3：考虑并行任务
此示例根据任务数调整池大小。 此公式还考虑为池设置的 [MaxTasksPerComputeNode][net_maxtasks] 值。 在对池启用了[并行任务执行](batch-parallel-node-tasks.md)的情况下，此方法特别有效。

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>示例 4：设置初始池大小
此示例演示了一个 C# 代码片段，其中的自动缩放公式可在初始时间段内将池大小设置为指定的节点数。 然后，在初始时间段过后，该公式会根据正在运行和处于活动状态的任务的数目调整池大小。

以下代码片段中的公式：

* 将初始池大小设置为 4 个节点。
* 在池生命周期的最初 10 分钟内不调整池大小。
* 10 分钟后，获取过去 60 分钟内正在运行和处于活动状态的任务数目的最大值。
  * 如果这两个值均为 0（表示过去 60 分钟没有正在运行或处于活动状态的任务），则池大小将设置为 0。
  * 如果其中一个值大于零，则不进行任何更改。

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>后续步骤
* [通过并发节点任务最大限度地提高 Azure Batch 计算资源的利用率](batch-parallel-node-tasks.md)详细说明了如何在池中的计算节点上同时执行多个任务。 除了自动缩放以外，此功能还可帮助降低某些工作负荷的作业持续时间，从而节省资金。
* 为了进一步提升效率，请确保 Batch 应用程序以最佳的方式查询 Batch 服务。 请参阅[有效地查询 Azure Batch 服务](batch-efficient-list-queries.md)，了解在查询数千个计算节点或任务的状态时，如何限制跨线数据量。

[net_api]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[net_batchclient]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient
[net_cloudpool_autoscaleformula]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula
[net_cloudpool_autoscaleevalinterval]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval
[net_enableautoscaleasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync
[net_maxtasks]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.maxtaskspercomputenode
[net_poolops_resizepoolasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync

[rest_api]: https://docs.microsoft.com/rest/api/batchservice/
[rest_autoscaleformula]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_autoscaleinterval]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_enableautoscale]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
