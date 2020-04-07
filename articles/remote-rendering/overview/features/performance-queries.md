---
title: 服务器端性能查询
description: 如何通过 API 调用执行服务器端性能查询
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 9a28dee2d1e6d1355b729a56e8eeb8447e4ed8c8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682021"
---
# <a name="server-side-performance-queries"></a>服务器端性能查询

良好的服务器上的渲染性能对于稳定的帧速率和良好的用户体验至关重要。 仔细监视服务器上的性能特征并在必要时进行优化非常重要。 性能数据可以通过专用 API 功能进行查询。

对渲染性能影响最大的是模型输入数据。 您可以调整输入数据，如[配置模型转换中](../../how-tos/conversion/configure-model-conversion.md)所述。

客户端应用程序性能也可能成为瓶颈。 要深入分析客户端性能，建议采用[性能跟踪](../../how-tos/performance-tracing.md)。

## <a name="clientserver-timeline"></a>客户端/服务器时间线

在详细介绍各种延迟值之前，有必要查看时间线上的客户端和服务器之间的同步点：

![管道时间线](./media/server-client-timeline.png)

下图显示了如何：

* *a Pose 估计*由客户端以恒定的 60 Hz 帧速率（每 16.6 毫秒）开始
* 然后，服务器根据姿势开始渲染
* 服务器发送回编码的视频图像
* 客户端解码图像，在映像之上执行一些 CPU 和 GPU 工作，然后显示图像

## <a name="frame-statistics-queries"></a>帧统计信息查询

帧统计信息为最后一帧提供了一些高级信息，例如延迟。 结构中`FrameStatistics`提供的数据在客户端进行测量，因此 API 是同步调用：

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
| 延迟接收 | 来自摄像机的延迟在客户端设备上进行估计，直到此姿势的服务器帧完全可供客户端应用程序使用。 此值包括网络往返、服务器渲染时间、视频解码和抖动补偿。 请参阅**上图中的间隔 1。**|
| 延迟接收到存在 | 从接收的远程帧的可用性到客户端应用在 CPU 上调用呈现帧的延迟。 |
| 延迟显示  | 从在 CPU 上显示帧到显示亮起的延迟。 此值包括客户端 GPU 时间、操作系统执行的任何帧缓冲、硬件重新投影和与设备相关的显示扫描时间。 请参阅**上图中的间隔 2。**|
| 时间 自上次存在 | 后续调用 CPU 上的"当前帧"之间的时间。 大于显示持续时间的值（例如 60 Hz 客户端设备上的 16.6 ms）表示客户端应用程序未能及时完成 CPU 工作负载所造成的问题。 请参阅**上图中的间隔 3。**|
| 视频帧接收 | 在最后一秒从服务器接收的帧数。 |
| 视频帧重新使用计数 | 设备上多次使用的接收帧数。 非零值表示由于网络抖动或服务器渲染时间过长，必须重复使用和重新投影帧。 |
| 跳过的视频帧 | 已解码的最后一秒接收的帧数，但由于较新的帧已到达，因此未显示。 非零值表示网络抖动导致多个帧延迟，然后一起到达客户端设备。 |
| 视频帧已丢弃 | 非常类似于**视频FrameS跳过**，但被丢弃的原因是，一个帧来得太晚，它甚至不能与任何挂起的姿势关联了。 如果发生这种情况，则存在一些严重的网络争用。|
| 视频框架明三角洲 | 在最后一秒到达的两个连续帧之间的最短时间量。 与视频FrameMaxDelta一起，此范围可指示由网络或视频编解码器引起的抖动。 |
| 视频FrameMaxDelta | 在最后一秒到达的两个连续帧之间的最大时间量。 与视频FrameMinDelta一起，此范围可指示由网络或视频编解码器引起的抖动。 |

所有延迟值的总和通常比 60 Hz 的可用帧时间大得多。 这没关系，因为多个帧并行飞行，并且新的帧请求以所需的帧速率启动，如图所示。 但是，如果延迟变得太大，它会影响[后期重新投影](../../overview/features/late-stage-reprojection.md)的质量，并可能危及整体体验。

`videoFramesReceived``videoFrameReusedCount`，`videoFramesDiscarded`并可用于测量网络和服务器性能。 如果`videoFramesReceived`低且`videoFrameReusedCount`高，则表示网络拥塞或服务器性能不佳。 高`videoFramesDiscarded`值还表示网络拥塞。

最后，`timeSinceLastPresent` `videoFrameMinDelta`，`videoFrameMaxDelta`并给出传入视频帧和本地当前调用的方差。 高方差意味着帧速率不稳定。

上述值均未明确指示纯网络延迟（图中的红色箭头），因为需要从往返值`latencyPoseToReceive`中减去服务器正忙于渲染的确切时间。 总体延迟的服务器端部分是客户端不可用的信息。 但是，下一段将解释如何通过来自服务器的其他输入并通过`networkLatency`该值公开来估计此值。

## <a name="performance-assessment-queries"></a>绩效评估查询

*性能评估查询*提供有关服务器上的 CPU 和 GPU 工作负载的更深入信息。 由于从服务器请求数据，因此查询性能快照遵循通常的异步模式：

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

与`FrameStatistics`对象相反，`PerformanceAssessment`该对象包含服务器端信息：

| 成员 | 说明 |
|:-|:-|
| 时间CPU | 每帧平均服务器 CPU 时间（以毫秒为单位） |
| 时间GPU | 每帧平均服务器 GPU 时间（以毫秒为单位） |
| 利用率CPU | 服务器 CPU 总利用率（百分比） |
| 利用 | 服务器 GPU 总利用率（百分比） |
| 内存CPU | 服务器主内存总利用率（百分比） |
| 内存GPU | 以服务器 GPU 百分比表示的专用视频内存利用率 |
| 网络延迟 | 近似平均往返网络延迟（以毫秒为单位）。 在上面的插图中，这对应于红色箭头的总和。 该值是通过从 的值中减去实际服务器呈现时间来计算`latencyPoseToReceive`的`FrameStatistics`。 虽然此近似值不准确，但它提供了一些网络延迟的指示，与客户端上计算的延迟值隔离。 |
| 多边形渲染 | 在一个帧中呈现的三角形数。 此数字还包括稍后在渲染过程中剔除的三角形。 这意味着，此数字在不同的摄像机位置变化并不大，但性能可能会有很大差异，具体取决于三角形剔除率。|

为了帮助你评估值，每个部分都带有质量分类，如 **"伟大**、**良好**、**平庸**"或 **"坏**"。
此评估指标提供服务器运行状况的粗略指示，但不应将其视为绝对指标。 例如，假设您看到 GPU 时间的"平庸"分数。 它被认为是平庸的，因为它接近整个帧时间预算的极限。 但是，在您的情况下，它可能是一个不错的值，因为您正在呈现一个复杂的模型。

## <a name="statistics-debug-output"></a>统计调试输出

该类`ARRServiceStats`环绕帧统计信息和性能评估查询，并提供方便的功能，将统计信息返回为聚合值或预构建字符串。 以下代码是在客户端应用程序中显示服务器端统计信息的最简单方法。

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

上面的代码用以下文本填充文本标签：

![ArrServiceStats 字符串输出](./media/arr-service-stats.png)

`GetStatsString` API 设置所有值的字符串，但也可以从`ARRServiceStats`实例以编程方式查询每个值。

成员也有变体，这些变体会随时间聚合值。 请参阅后缀`*Avg`的成员 ，`*Max`或`*Total`。 该成员`FramesUsedForAverage`指示此聚合已使用多少帧。

## <a name="next-steps"></a>后续步骤

* [创建性能跟踪](../../how-tos/performance-tracing.md)
* [配置模型转换](../../how-tos/conversion/configure-model-conversion.md)
