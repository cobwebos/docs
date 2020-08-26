---
title: 服务器端性能查询
description: 如何通过 API 调用执行服务器端性能查询
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 3207ffca6fd0fbc943f4a2873b8b6c9029d565af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84022788"
---
# <a name="server-side-performance-queries"></a>服务器端性能查询

要提供稳定的帧速率和良好的用户体验，服务器上必须具有良好的渲染性能。 请务必谨慎监视服务器上的性能特性，并在必要时进行优化。 可通过专用 API 函数查询性能数据。

对渲染性能影响最大的是模型输入数据。 可按[配置模型转换](../../how-tos/conversion/configure-model-conversion.md)中描述的方式调整输入数据。

客户端应用程序性能也可能成为瓶颈。 若要深入分析客户端性能，建议采用 [:::no-loc text="performance trace":::](../../how-tos/performance-tracing.md) 。

## <a name="clientserver-timeline"></a>客户端/服务器时间线

在详细讨论各种延迟值之前，有必要在时间线上查看客户端和服务器之间的同步点：

![管道时间线](./media/server-client-timeline.png)

上图显示了操作方式：

* 客户端以恒定的 60 Hz 帧速率（每 16.6 毫秒）开始姿态估计
* 服务器随后根据姿态开始渲染
* 服务器发回已编码的视频图像
* 客户端对图像进行解码，根据它执行一些 CPU 和 GPU 工作，然后显示图像

## <a name="frame-statistics-queries"></a>帧统计信息查询

帧统计信息提供了关于最后一帧的一些大致信息，例如延迟。 `FrameStatistics` 结构中提供的数据是在客户端测量的，因此 API 是同步调用：

```cs
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
```

```cpp
void QueryFrameData(ApiHandle<AzureSession> session)
{
    FrameStatistics frameStatistics;
    if (*session->GetGraphicsBinding()->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        // do something with the result
    }
}
```

检索到的 `FrameStatistics` 对象包含以下成员：

| 成员 | 说明 |
|:-|:-|
| latencyPoseToReceive | 从客户端设备上的相机姿态估计一直到该姿态的服务器帧可完全用于客户端应用程序之间的延迟。 该值包括网络往返、服务器渲染、视频解码和抖动补偿时间。 请查看上图中的时间间隔 1。|
| latencyReceiveToPresent | 从收到的远程帧可用一直到客户端应用在 CPU 上调用 PresentFrame 之间的延迟。 |
| latencyPresentToDisplay  | 从在 CPU 上显示一个帧一直到显示屏亮起之间的延迟。 该值包括客户端 GPU 时间、操作系统执行的任何帧缓冲、硬件重新投影和与设备相关的显示屏扫描输出时间。 请查看上图中的时间间隔 2。|
| timeSinceLastPresent | 在 CPU 上对 PresentFrame 进行后续调用之间的时间。 如果值大于显示持续时间（例如，在 60 Hz 客户端设备上为 16.6 毫秒），则表示存在客户端应用程序未及时完成其 CPU 工作负载所造成的问题。 请查看上图中的时间间隔 3。|
| videoFramesReceived | 最后一秒内从服务器接收的帧数。 |
| videoFrameReusedCount | 最近一秒在设备上多次使用的已接收帧数。 如果值不是零，则表示由于网络抖动或服务器渲染时间过长，必须重新使用和重新投影帧。 |
| videoFramesSkipped | 在最后一秒被解码但由于新帧到达而未显示的已接收帧数。 如果值不是零，则表示网络抖动导致多个帧延迟，然后以突发形式一起到达客户端设备。 |
| videoFramesDiscarded | 与 videoFramesSkipped 非常相似，但是被丢弃的原因是帧到达时间太晚，以至于它甚至不能与任何挂起的姿态相关联。 如果发生这种情况，则会出现严重的网络争用。|
| videoFrameMinDelta | 在最后一秒内到达的两个连续帧之间的最短时间。 此范围结合 videoFrameMaxDelta，表示抖动是由网络或视频编解码器引起的。 |
| videoFrameMaxDelta | 在最后一秒内到达的两个连续帧之间的最长时间。 此范围结合 videoFrameMinDelta，表示抖动是由网络或视频编解码器引起的。 |

所有延迟值的总和通常远大于 60 Hz 下的可用帧时间。 这很正常，因为多个帧并行传输，而新帧请求按所需的帧速率启动，如图中所示。 然而，如果延迟太大，就会影响[后期阶段的重新投影](../../overview/features/late-stage-reprojection.md)的质量，并可能降低整体体验。

`videoFramesReceived`、`videoFrameReusedCount` 和 `videoFramesDiscarded` 可用于测量网络和服务器的性能。 如果 `videoFramesReceived` 较低且 `videoFrameReusedCount` 较高，则可能表示网络拥塞或服务器性能不佳。 `videoFramesDiscarded` 值高也表示网络拥塞。

最后，`timeSinceLastPresent``videoFrameMinDelta` 和 `videoFrameMaxDelta` 给出了传入视频帧和本地当前调用之间有差异这一概念。 差异大表示帧速率不稳定。

上面的值均未明确指示纯网络延迟（图中的红色箭头），因为需要从往返值 `latencyPoseToReceive` 中减去服务器忙于渲染的确切时间。 总延迟中的服务器端信息不可用于客户端。 不过，下一段落将说明如何通过来自服务器的其他输入估算此值并通过 `networkLatency` 值进行公开。

## <a name="performance-assessment-queries"></a>性能评估查询

可通过性能评估查询更深入地了解服务器上的 CPU 和 GPU 工作负载。 由于已从服务器请求数据，因此查询性能快照时遵循通常的异步模式：

```cs
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

```cpp
void QueryPerformanceAssessment(ApiHandle<AzureSession> session)
{
    ApiHandle<PerformanceAssessmentAsync> assessmentQuery = *session->Actions()->QueryServerPerformanceAssessmentAsync();
    assessmentQuery->Completed([] (ApiHandle<PerformanceAssessmentAsync> res)
    {
        // do something with the result:
        PerformanceAssessment result = *res->Result();
        // ...

    });
}
```

与 `FrameStatistics` 对象相反，`PerformanceAssessment` 对象包含服务器端信息：

| 成员 | 说明 |
|:-|:-|
| timeCPU | 每帧平均服务器 CPU 时间（毫秒） |
| timeGPU | 每帧平均服务器 GPU 时间（毫秒） |
| utilizationCPU | 服务器 CPU 总使用率（百分比） |
| utilizationGPU | 服务器 GPU 总使用率（百分比） |
| memoryCPU | 服务器主内存总使用率（百分比） |
| memoryGPU | 专用视频内存的总使用率（占服务器 GPU 的百分比） |
| networkLatency | 近似平均往返网络延迟（毫秒）。 在上图中，对应于红色箭头的总和。 该值是通过 `FrameStatistics` 的 `latencyPoseToReceive` 值减去实际服务器渲染时间来计算的。 尽管该近似值不准确，但它提供了网络延迟的某个指示，与客户端上计算的延迟值不相关。 |
| polygonsRendered | 在一帧中渲染的三角形的数目。 此数字还包括稍后在渲染过程中剔除的三角形。 这意味着，即使相机位置不同，该数字也变化不大，但是性能可能会大幅度变化，具体取决于三角形剔除率。|

为了帮助你评估这些值，每个部分都有质量分类，例如“很好”、“好”、“一般”或“差”   。
此评估指标可粗略指示服务器运行状况，但不得将它视为绝对指标。 例如，假设看到 GPU 时间的得分为“一般”。 它被认为是中等性能，因为它接近整个帧时间预算的限制。 然而，对你来说这个值可能还不错，因为你正在渲染一个复杂的模型。

## <a name="statistics-debug-output"></a>统计信息调试输出

类 `ARRServiceStats` 是一个 C# 类，它包装了帧统计信息和性能评估查询，并提供了方便的功能来以聚合值或预构建字符串的形式返回统计信息。 要在客户端应用程序中显示服务器端统计信息，最简单方法是使用以下代码。

```cs
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

上述代码将用以下文本填充文本标签：

![ArrServiceStats 字符串输出](./media/arr-service-stats.png)

`GetStatsString` API 格式化所有值的字符串，但你也可从 `ARRServiceStats` 实例中以编程方式查询单独的每个值。

还存在一些成员的变体，它们将在一段时间内聚合这些值。 请查看具有 `*Avg`、`*Max` 或 `*Total` 后缀的成员。 成员 `FramesUsedForAverage` 指示已用于此聚合的帧数。

## <a name="next-steps"></a>后续步骤

* [创建性能跟踪](../../how-tos/performance-tracing.md)
* [配置模型转换](../../how-tos/conversion/configure-model-conversion.md)
