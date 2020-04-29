---
title: 服务器端性能查询
description: 如何通过 API 调用执行服务器端性能查询
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 9a28dee2d1e6d1355b729a56e8eeb8447e4ed8c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80682021"
---
# <a name="server-side-performance-queries"></a>服务器端性能查询

服务器上的良好渲染性能对于稳定的帧速率和良好的用户体验至关重要。 务必仔细监视服务器上的性能特性，并在必要时进行优化。 可以通过专用 API 函数查询性能数据。

呈现性能的大多数有影响力是模型输入数据。 您可以根据[配置模型转换](../../how-tos/conversion/configure-model-conversion.md)中所述调整输入数据。

客户端应用程序性能也可能是瓶颈。 若要深入分析客户端性能，建议采用[性能跟踪](../../how-tos/performance-tracing.md)。

## <a name="clientserver-timeline"></a>客户端/服务器时间线

在深入了解各种滞后时间值之前，有必要查看时间线上的客户端和服务器之间的同步点：

![管道时间线](./media/server-client-timeline.png)

下图显示了如何：

* 客户端启动的*姿势估计*值为 60-Hz 帧速率（每16.6 毫秒）
* 然后，服务器会根据姿势开始呈现
* 服务器发回编码的视频图像
* 客户端对映像进行解码，在其顶部执行一些 CPU 和 GPU 工作，并显示映像

## <a name="frame-statistics-queries"></a>帧统计信息查询

帧统计信息为最后一帧提供一些高级信息，如延迟。 `FrameStatistics`结构中提供的数据在客户端进行测量，因此 API 是同步调用：

````c#
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
````

检索`FrameStatistics`的对象包含以下成员：

| 成员 | 说明 |
|:-|:-|
| latencyPoseToReceive | 相机的延迟在客户端设备上提出估计，直到此姿势的服务器帧完全可用于客户端应用程序。 此值包括网络往返、服务器呈现时间、视频解码和抖动补偿。 请参阅**上图中的时间间隔1。**|
| latencyReceiveToPresent | 收到的远程帧的可用性延迟，直到客户端应用在 CPU 上调用 PresentFrame。 |
| latencyPresentToDisplay  | 在 CPU 上呈现帧之前的延迟，直到显示亮起。 此值包括客户端 GPU 时间、操作系统所执行的任何帧缓冲、硬件 reprojection 和设备相关的显示扫描时间。 请参阅**上图中的 "间隔 2"。**|
| timeSinceLastPresent | 对 CPU 的后续调用之间的时间间隔（PresentFrame）。 大于显示持续时间（例如，60-Hz 客户端设备上的 16.6 ms）的值表示由客户端应用程序无法及时完成其 CPU 工作负荷导致的问题。 请参阅**上图中的时间间隔3。**|
| videoFramesReceived | 上一秒从服务器接收的帧数。 |
| videoFrameReusedCount | 最近一次在设备上使用的收到的帧数。 非零值表示必须重复使用帧，因为网络抖动或服务器渲染时间过多 reprojected。 |
| videoFramesSkipped | 在最后一秒已解码但未显示的帧数，因为较新帧已到达。 非零值表示网络 jittering 导致了多个帧延迟，然后在突发时进入客户端设备。 |
| videoFramesDiscarded | 与**videoFramesSkipped**非常相似，但被丢弃的原因是，帧延迟，甚至不能再与任何挂起的姿势关联。 如果发生这种情况，则会出现严重的网络争用情况。|
| videoFrameMinDelta | 在最后一秒到达两个连续帧之间的最小时间长度。 此范围与 videoFrameMaxDelta 一起提供了由网络或视频编解码器引起的抖动的指示。 |
| videoFrameMaxDelta | 在最后一秒到达两个连续帧之间的最大时间长度。 此范围与 videoFrameMinDelta 一起提供了由网络或视频编解码器引起的抖动的指示。 |

所有滞后时间值的总和通常远远大于可用帧时间 60 Hz。 这是正常的，因为多个帧以并行方式进行，而新的帧请求将以所需的帧速率关闭，如图中所示。 但是，如果滞后时间太大，会影响 reprojection 的最[晚](../../overview/features/late-stage-reprojection.md)质量，并且可能会危及总体体验。

`videoFramesReceived`、 `videoFrameReusedCount`和`videoFramesDiscarded`可用于估量网络和服务器性能。 如果`videoFramesReceived`为 low 并且`videoFrameReusedCount`很高，则可能表示网络拥塞或服务器性能不佳。 较高`videoFramesDiscarded`的值还表明网络拥塞。

最后是`timeSinceLastPresent`、 `videoFrameMinDelta`、和`videoFrameMaxDelta` ，它大致介绍了传入的视频帧和本地提供调用的变化。 高差异意味着 instable 帧速率。

以上任何值都不能明确指出纯网络延迟（图中的红色箭头），因为需要从往返值`latencyPoseToReceive`中减去服务器的正在进行的呈现的准确时间。 总体延迟的服务器端部分是客户端不可用的信息。 但下一段说明了如何通过来自服务器的其他输入并通过`networkLatency`值公开此值。

## <a name="performance-assessment-queries"></a>性能评估查询

*性能评估查询*提供有关服务器上的 CPU 和 GPU 工作负荷的更深入信息。 由于已从服务器请求数据，因此查询性能快照时遵循常见的异步模式：

``` cs
PerformanceAssessmentAsync _assessmentQuery = null;

void QueryPerformanceAssessment(AzureSession session)
{
    _assessmentQuery = session.Actions.QueryServerPerformanceAssessmentAsync();
    _assessmentQuery.Completed += (PerformanceAssessmentAsync res) =>
    {
        // do something with the result:
        PerformanceAssessment result = res.Result;
        // ...

        _assessmentQuery = null;
    };
}
```

与`FrameStatistics`对象相反， `PerformanceAssessment`对象包含服务器端信息：

| 成员 | 说明 |
|:-|:-|
| timeCPU | 每帧平均服务器 CPU 时间（毫秒） |
| timeGPU | 每帧平均服务器 GPU 时间（毫秒） |
| utilizationCPU | 服务器总 CPU 使用率（百分比） |
| utilizationGPU | 服务器 GPU 总使用率（百分比） |
| memoryCPU | 总体服务器主内存使用率（百分比） |
| memoryGPU | 服务器 GPU 的总专用视频内存使用率（百分比） |
| networkLatency | 近似平均往返网络延迟（毫秒）。 在上图中，这对应于红色箭头的总和。 值是通过从的`latencyPoseToReceive` `FrameStatistics`值中减去实际服务器渲染时间来计算的。 尽管此近似值不准确，但它提供了一些网络延迟的指示，这与客户端上计算的滞后时间值相隔离。 |
| polygonsRendered | 在一帧中呈现的三角形的数目。 此数字还包括在呈现期间剔除的三角形。 这意味着，此数字在不同的相机位置上不会有很大的变化，但性能可能会有很大的差异，具体取决于三角剔除率。|

为帮助您评估这些值，每个部分都附带质量分类，**如****良好、良好**、**普通**或**不良**。
此评估指标提供服务器的健康状况，但不应将其视为绝对指标。 例如，假设您看到 GPU 时间的 "普通" 分数。 它被视为普通，因为它接近总体帧时间预算的限制。 但在您的情况下，它可能是一个不错的值，因为您要呈现复杂的模型。

## <a name="statistics-debug-output"></a>统计信息调试输出

类`ARRServiceStats`环绕帧统计信息和性能评估查询，并提供方便的功能，以将统计信息作为聚合值或预生成的字符串返回。 以下代码是在客户端应用程序中显示服务器端统计信息的最简单方法。

``` cs
ARRServiceStats _stats = null;

void OnConnect()
{
    _stats = new ARRServiceStats();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

上面的代码将用以下文本填充文本标签：

![ArrServiceStats 字符串输出](./media/arr-service-stats.png)

`GetStatsString` API 设置所有值的字符串的格式，但也可以从`ARRServiceStats`实例以编程方式查询每个值。

还有一些成员的变体，它们聚合了一段时间内的值。 请参阅具有后缀`*Avg`、 `*Max`或`*Total`的成员。 成员`FramesUsedForAverage`指示用于此聚合的帧数。

## <a name="next-steps"></a>后续步骤

* [创建性能跟踪](../../how-tos/performance-tracing.md)
* [配置模型转换](../../how-tos/conversion/configure-model-conversion.md)
