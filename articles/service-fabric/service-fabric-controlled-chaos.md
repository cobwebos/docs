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
ms.date: 02/05/2018
ms.author: motanv
ms.openlocfilehash: 81206257cb2c7157bbb1ffcf3a79ced7c896ef80
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>在 Service Fabric 群集中引入受控的混沌测试
大规模分布式系统，例如云基础结构，在本质上都是不可靠的。 Azure Service Fabric 可让开发人员在不可靠的基础结构之上编写可靠的分布式服务。 若要在不可靠的基础结构之上编写可靠的分布式服务，开发人员应能够在不可靠的底层基础结构因故障而进行复杂的状态转换时，测试其服务的稳定性。

[故障注入和群集分析服务](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview)（也称为故障分析服务）使开发人员能够引入故障来测试其服务。 这些定向模拟故障（如[重新启动分区](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps)）可以帮助执行最常见的状态转换。 但是，定向模拟故障易被定义左右，因此可能会漏掉仅在一系列难以预测、持续时间长且复杂的状态转换中出现的 bug。 若要进行无偏测试，可以使用混沌测试。

混沌测试在整个群集模拟定期交叉出现的故障，包括常规故障和非常规故障，时间跨度很长。 常规故障由一组 Service Fabric API 调用组成，例如，重启副本故障是常规故障，因为这次关闭后接着副本打开。 删除副本、移动主要副本和移动次要副本是混沌测试执行的其他常规故障。 非常规故障是进程退出，例如重启节点和重启代码包。 

配置混沌测试的故障率和故障类型后，就可以通过 C#、Powershell 或 REST API 启动该测试，开始在群集和服务中生成故障。 可以将混沌测试配置为运行一段指定的时间（例如运行一个小时），此时间段过后，混沌测试自动停止，也可以随时调用 StopChaos API（C#、Powershell 或 REST）来停止混沌测试。

> [!NOTE]
> 从目前来看，混沌测试只会引入安全的故障，这意味着，在没有外部故障的情况下，绝对不会发生仲裁丢失或数据丢失。
>

混沌测试在运行时，将生成不同的事件来捕获当前的运行状态。 例如，ExecutingFaultsEvent 包含混沌测试决定在该迭代中执行的所有故障。 ValidationFailedEvent 包含群集验证期间发现的验证故障（运行状况或稳定性问题）的详细信息。 可以调用 GetChaosReport API（C#、Powershell 或 REST）来获取混沌测试运行报告。 这些事件保存在一个 [Reliable Dictionary](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-reliable-collections) 中，该字典的截断策略由两项配置决定：**MaxStoredChaosEventCount**（默认值为 25000）和 **StoredActionCleanupIntervalInSeconds**（默认值为 3600）。 混沌测试每隔 *StoredActionCleanupIntervalInSeconds* 进行一次检查，从 Reliable Dictionary 中清除除最新 *MaxStoredChaosEventCount* 事件以外的所有事件。

## <a name="faults-induced-in-chaos"></a>在混沌测试中引入的故障
混沌测试在整个 Service Fabric 群集中生成故障，将几个月或几年内出现的故障压缩成几小时。 通过将各种具有高故障率的交叉故障组合在一起，能够找出很有可能被忽视的极端状况。 运行这种混沌测试会使服务的代码质量得到显著提高。

混沌测试引入以下类别的故障：

* 重新启动节点
* 重新启动已部署的代码包
* 删除副本
* 重新启动副本
* 移动主副本（可配置）
* 移动辅助副本（可配置）

混沌测试在多个迭代中运行。 每个迭代包含指定时间段的故障和群集验证。 可以配置使群集达到稳定状态以及成功完成验证所用的时间。 如果在群集验证中发现故障，混沌测试将生成并保留一个包含 UTC 时间戳与故障详细信息的 ValidationFailedEvent。 例如，假设某个混沌测试实例设置为运行 1 小时，并且最多有 3 个并发故障。 该混沌测试将引入三个故障，然后验证群集运行状况。 它会循环访问上一步骤，直到通过 StopChaosAsync API 或者在一小时后将它显式停止。 如果在任何一次迭代中群集变得不正常（即在传入的 MaxClusterStabilizationTimeout 内不稳定或未变得正常），混沌测试会生成 ValidationFailedEvent。 此事件指明系统出现问题，可能需要进一步调查。

若要获取混沌测试引入的故障，可以使用 GetChaosReport API（powershell、C# 或 REST）。 该 API 根据传入的继续标记或传入的时间范围获取混沌测试报告的下一段。 可以指定 ContinuationToken 以获取混沌测试报告的下一段或者通过 StartTimeUtc 和 EndTimeUtc 指定时间范围，但不能在同一调用中同时指定 ContinuationToken 和时间范围。 如果发生了 100 个以上的混沌测试事件，混沌测试报告会分段返回，每一段包含的混沌测试事件均不能超过 100 个。

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

* **EnableMoveReplicaFaults**：启用或禁用导致主副本或辅助副本移动的故障。 默认情况下，这些故障处于启用状态。
* **WaitTimeBetweenIterations**：每两次迭代之间的等待时间。 即在执行一轮故障并完成群集运行状况的相应验证之后，混沌测试将暂停的时长。 该值越高，平均故障注入速率就越低。
* **WaitTimeBetweenFaults**：单个迭代中每两次连续故障之间的等待时间。 该值越高，故障的并发性（即故障之间的重叠）越低。
* **ClusterHealthPolicy**：群集运行状况策略用于验证两次混沌测试迭代之间的群集运行状况。 如果群集运行状况出错或者如果在故障执行期间发生了意外的异常，混沌测试会等 30 分钟后再进行下一轮运行状况检查，从而为群集留出一些恢复时间。
* **Context**：一组 (string, string) 类型的键值对。 此映射可用于记录混沌测试的相关运行信息。 这种键值对不能超过 100 个，并且每个字符串（键或值）的长度不能超过 4095 个字符。 此映射由混沌测试运行的启动程序设置为根据需要存储特定运行的相关上下文。
* **ChaosTargetFilter**：此筛选器可指定混沌测试故障仅面向特定节点类型或特定应用程序实例。 如未使用 ChaosTargetFilter，混沌测试会使所有群集实体故障。 如果使用 ChaosTargetFilter，混沌测试仅使满足 ChaosTargetFilter 规定的实体故障。 NodeTypeInclusionList 和 ApplicationInclusionList 仅允许联合语义。 换句话说，不可指定 NodeTypeInclusionList 和 ApplicationInclusionList 的交集。 例如，不可指定“仅当此应用程序在该节点类型上时使其故障”。 一旦实体包含在 NodeTypeInclusionList 或 ApplicationInclusionList 中，便不能使用 ChaosTargetFilter 排除该实体。 即使 applicationX 未出现在 ApplicationInclusionList 中，在一些混沌测试迭代中，也可使 applicationX 故障，因为它恰好在 NodeTypeInclusionList 中的 nodeTypeY 的节点上。 如果 NodeTypeInclusionList 和 ApplicationInclusionList 都为 null 或者为空，则会引发 ArgumentException。
    * **NodeTypeInclusionList**：包括在混沌测试故障中的节点类型列表。 所有类型故障（重启节点、重启代码包、删除副本、重启副本、移动主副本和移动辅助副本）均为这些节点类型的节点启用。 如果节点类型（比如 NodeTypeX）未出现在 NodeTypeInclusionList 中，节点级别故障（比如 NodeRestart）将不会为 NodeTypeX 的节点启用。但是，如果 ApplicationInclusionList 中的应用程序碰巧位于 NodeTypeX 的节点上，那么代码包和副本故障仍可为 NodeTypeX 启用。 此列表最多可以包含 100 个节点类型名称，若要增加，MaxNumberOfNodeTypesInChaosTargetFilter 配置需要升级。
    * **ApplicationInclusionList**：包含在混沌测试故障中的应用程序 URI 列表。 所有属于这些应用程序服务的副本服从混沌测试的副本故障（重启副本、删除副本、移动主副本和移动辅助副本）。 仅在代码包仅托管这些应用程序的副本时，混沌测试可重启代码包。 如果应用程序未出现在此列表中，那么还是可以在某些混沌测试迭代中使它故障，条件是应用程序最终位于 NodeTypeInclusionList 中的节点类型的节点上。 但是，如果 applicationX 通过放置约束固定为 nodeTypeY，并且 applicationX 不在 ApplicationInclusionList 中同时 nodeTypeY 不在 NodeTypeInclusionList 中，那么不会使 applicationX 故障。 此列表最多可以包含 1000 个应用程序名称，若要增加，MaxNumberOfApplicationsInChaosTargetFilter 配置需要升级。

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

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
            // During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
            // the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration. 
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica, and move secondary replica will happen
            // for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList, 
            // restart code package, restart replica, move primary replica, move secondary replica faults will happen for 'fabric:/TestApp2'
            // even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is not of type included in nodeypeInclusionList.
            var applicationInclusionList = new List<string> { "fabric:/TestApp2" };

            // List of cluster entities to target for Chaos faults.
            var chaosTargetFilter = new ChaosTargetFilter
            {
                NodeTypeInclusionList = nodetypeInclusionList,
                ApplicationInclusionList = applicationInclusionList
            };

            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy) {ChaosTargetFilter = chaosTargetFilter};

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

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

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
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy. 
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration. 
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
# the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move secondary replica will happen
# for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for 'fabric:/TestApp2'
# even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is not of type included in nodeypeInclusionList.
$chaosTargetFilter.ApplicationInclusionList.Add("fabric:/TestApp2")

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy -ChaosTargetFilter $chaosTargetFilter

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
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}
```
