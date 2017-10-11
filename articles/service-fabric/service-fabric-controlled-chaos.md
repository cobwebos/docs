---
title: "在 Service Fabric 群集中引入混沌测试 | Microsoft 文档"
description: "使用故障注入和群集分析服务 API 管理群集中的混沌测试。"
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: motanv
ms.openlocfilehash: 3b3b93bc9ec5ecdcfc289e5b62e84de6aa4172ed
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>在 Service Fabric 群集中引入受控的混沌测试
大规模分布式系统，例如云基础结构，在本质上都是不可靠的。 Azure Service Fabric 可让开发人员在不可靠的基础结构之上编写可靠的分布式服务。 若要在不可靠的基础结构之上编写可靠的分布式服务，开发人员应能够在不可靠的底层基础结构因故障而进行复杂的状态转换时，测试其服务的稳定性。

[故障注入和群集分析服务](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-testability-overview)（也称为故障分析服务）使开发人员能够引入故障来测试其服务。 这些定向模拟故障（如[重新启动分区](https://docs.microsoft.com/en-us/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps)）可以帮助执行最常见的状态转换。 但是，定向模拟故障易被定义左右，因此可能会漏掉仅在一系列难以预测、持续时间长且复杂的状态转换中出现的 bug。 若要进行无偏测试，可以使用混沌测试。

混沌测试在整个群集模拟定期交叉出现的故障，包括常规故障和非常规故障，时间跨度很长。 配置混沌测试的故障率和故障类型后，就可以通过 C# 或 PowerShell API 启动该测试，开始在群集和服务中生成故障。 可以将混沌测试配置为运行一段指定的时间（例如运行一个小时），此时间段过后，混沌测试自动停止，也可以随时调用 StopChaos API（C# 或 Powershell）来停止混沌测试。

> [!NOTE]
> 从目前来看，混沌测试只会引入安全的故障，这意味着，在没有外部故障的情况下，绝对不会发生仲裁丢失或数据丢失。
>

混沌测试在运行时，将生成不同的事件来捕获当前的运行状态。 例如，ExecutingFaultsEvent 包含混沌测试决定在该迭代中执行的所有故障。 ValidationFailedEvent 包含群集验证期间发现的验证故障（运行状况或稳定性问题）的详细信息。 可以调用 GetChaosReport API（C# 或 Powershell）来获取混沌测试运行报告。 这些事件保存在一个 [Reliable Dictionary](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-reliable-collections) 中，该字典的截断策略由两项配置决定：**MaxStoredChaosEventCount**（默认值为 25000）和 **StoredActionCleanupIntervalInSeconds**（默认值为 3600）。 混沌测试每隔 *StoredActionCleanupIntervalInSeconds* 进行一次检查，从 Reliable Dictionary 中清除除最新 *MaxStoredChaosEventCount* 事件以外的所有事件。

## <a name="faults-induced-in-chaos"></a>在混沌测试中引入的故障
混沌测试在整个 Service Fabric 群集中生成故障，将几个月或几年内出现的故障压缩成几小时。 通过将各种具有高故障率的交叉故障组合在一起，能够找出很有可能被忽视的极端状况。 运行这种混沌测试会使服务的代码质量得到显著提高。

混沌测试引入以下类别的故障：

* 重新启动节点
* 重新启动已部署的代码包
* 删除副本
* 重新启动副本
* 移动主副本（可配置）
* 移动辅助副本（可配置）

混沌测试在多个迭代中运行。 每个迭代包含指定时间段的故障和群集验证。 可以配置使群集达到稳定状态以及成功完成验证所用的时间。 如果在群集验证中发现故障，混沌测试将生成并保留一个包含 UTC 时间戳与故障详细信息的 ValidationFailedEvent。 例如，假设某个混沌测试实例设置为运行 1 小时，并且最多有 3 个并发故障。 该混沌测试将引入三个故障，然后验证群集运行状况。 它会循环访问上一步骤，直到通过 StopChaosAsync API 或者在一小时后将它显式停止。 如果在任何一次迭代中群集变得不正常（即在传入的 MaxClusterStabilizationTimeout 内不稳定），混沌测试会生成 ValidationFailedEvent。 此事件指明系统出现问题，可能需要进一步调查。

若要获取混沌测试引入的故障，可以使用 GetChaosReport API（powershell 或 C#）。 该 API 根据传入的继续标记或传入的时间范围获取混沌测试报告的下一段。 可以指定 ContinuationToken 以获取混沌测试报告的下一段或者通过 StartTimeUtc 和 EndTimeUtc 指定时间范围，但不能在同一调用中同时指定 ContinuationToken 和时间范围。 如果发生了 100 个以上的混沌测试事件，混沌测试报告会分段返回，每一段包含的混沌测试事件均不能超过 100 个。

## <a name="important-configuration-options"></a>重要的配置选项
* **TimeToRun**：混沌测试在成功完成之前的总运行时间。 在混沌测试运行了 TimeToRun 这段时间之前，可以通过 StopChaos API 将它停止。

* **MaxClusterStabilizationTimeout**：在生成 ValidationFailedEvent 之前，等待群集变得正常的最长时间。 等待这段时间的目的是在恢复时减少群集上的负载。 执行的检查包括：
  * 群集运行状况是否正常
  * 服务运行状况是否正常
  * 服务分区是否达到目标副本集大小
  * 不存在 InBuild 副本
* **MaxConcurrentFaults**：在每个迭代中引入的最大并发故障数。 该数字越大，混沌测试就越激进，群集进行的故障转移和状态转换组合也更复杂。 

> [!NOTE]
> 无论 *MaxConcurrentFaults* 的值有多大，混沌测试都能保证在没有外部故障的情况下，不会发生仲裁丢失或数据丢失。
>

* **EnableMoveReplicaFaults**：启用或禁用导致主副本或辅助副本移动的故障。 默认情况下，这些故障处于禁用状态。
* **WaitTimeBetweenIterations**：每两次迭代之间的等待时间。 即在执行一轮故障并完成群集运行状况的相应验证之后，混沌测试将暂停的时长。 该值越高，平均故障注入速率就越低。
* **WaitTimeBetweenFaults**：单个迭代中每两次连续故障之间的等待时间。 该值越高，故障的并发性（即故障之间的重叠）越低。
* **ClusterHealthPolicy**：群集运行状况策略用于验证两次混沌测试迭代之间的群集运行状况。 如果群集运行状况出错或者如果在故障执行期间发生了意外的异常，混沌测试会等 30 分钟后再进行下一轮运行状况检查，从而为群集留出一些恢复时间。
* **Context**：一组 (string, string) 类型的键值对。 此映射可用于记录混沌测试的相关运行信息。 这种键值对不能超过 100 个，并且每个字符串（键或值）的长度不能超过 4095 个字符。 此映射由混沌测试运行的启动程序设置为根据需要存储特定运行的相关上下文。

## <a name="how-to-run-chaos"></a>如何运行混沌测试

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```
