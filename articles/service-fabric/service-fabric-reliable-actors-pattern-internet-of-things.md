
<properties
   pageTitle="物联网的 Reliable Actors"
   description="Service Fabric Reliable Actors 是系统中的关键构建基块（作为中间层），它结合了支持多种传输（如 HTTPS、MQTT 或 AMQP），然后与表示各个设备的参与者通信的消息传送系统前端。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/05/2015"
   wacn.date=""/>

# Reliable Actors 设计模式：物联网
由于 IoT 随着设备和云服务的技术进步而成为了新趋势，因此开发人员开始考虑用于开发其系统的关键构建基块。下图说明了使用 Service Fabric Reliable Actors 实现的重要方案：

![][1]

Service Fabric Reliable Actors 是系统中的关键构建基块（作为中间层），它结合了支持多种传输（如 HTTPS、MQTT 或 AMQP），然后与表示各个设备的参与者通信的消息传送系统前端。因为参与者可以维护状态，所以对流（尤其是有状态的流处理）进行建模和每个设备的聚合非常简单。如果必须长久保持数据，则我们还可以方便地按需或按计时器刷新，同时仍然可轻松地在另一个变量中保持最新的 N 位数据以便进行快速查询。请注意，在我们的示例中，我们有意省略了事件/消息传送层的详细信息（该层允许参与者与设备通信），以便重点介绍参与者模型。本质上通常是两种方案组合在一起：

* *从单个或一组设备收集遥测和状态数据并维护其状态*。考虑数万个在发送数据的捕鼠器（是的，这是真实的客户方案），发送的数据基本到设备是否在内部捕获了讨厌的害虫。数据按区域进行聚合，当在某个区域中捕获了足够的老鼠时，会分派工程师来清理设备。捕鼠器是否作为参与者？ 绝对是。每个区域的组参与者是否作为聚合器？ 当然是的。

* *将设备行为和配置推送到单个设备或一组设备*。考虑家用太阳能设备，其中供应商基于消耗模式和季节性推送不同配置。

## 遥测数据和设备分组

首先让我们来看一下设备（考虑数万个）组合在一起，并且都在向其关联组发送遥测数据的情况。在以下示例中，客户向每个区域部署了设备。当设备打开时，它执行的第一个操作是发送 ActivateMe 消息，其中包含相关信息（如位置、版本等）。与设备关联（通过设备 ID）的参与者进而会为设备设置初始状态，例如在本地保存状态（也可以长久保持）和注册组参与者。在此例中，我们为我们的模拟分配了一个随机组。

作为初始化过程的一部分，我们可以通过从组参与者或某些其他代理检索配置数据来配置设备。通过这种方式，设备在最初可能会非常笨拙，但在初始化时就会获得其“智能”。完成此操作后，设备和操作者已准备好发送和处理遥测数据。

所有设备都定期将其遥测信息发送给对应参与者。如果参与者已激活，则使用相同的参与者。否则会激活它。此时它可以从稳定存储中恢复状态（如果需要）。当参与者收到遥测信息时，它可以将信息存储到本地变量中。我们这样做以便简化示例。在实际实现中，我们可能会将它保存到外部存储以允许操作监视和诊断设备运行状况和性能。最后，我们将遥测数据推送到设备参与者在逻辑上所属的组参与者。

## IoT 代码示例 – 遥测

```csharp
public interface IThing : IActor
{
    Task ActivateMe(string region, int version);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingState
{
	[DataMember]
	public List<ThingTelemetry> _telemetry;
	[DataMember]
	public ThingInfo _deviceInfo;
	[DataMember]
	long _deviceGroupId;
}

public class Thing : Actor<ThingState>, IThing
{

    public override Task OnActivateAsync()
    {
        State._telemetry = new List<ThingTelemetry>();
        State._deviceGroupId = -1; // not activated
        return base.OnActivateAsync();
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        State._telemetry.Add(telemetry); // saving data at the device level
        if (State._deviceGroupId != -1)
        {
            var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
            return deviceGroup.SendTelemetryAsync(telemetry); // sending telemetry data for aggregation
        }
        return TaskDone.Done;
    }

    public Task ActivateMe(string region, int version)
    {
        State._deviceInfo = new ThingInfo()
        {
            DeviceId = this.GetPrimaryKeyLong(),
            Region = region,
            Version = version
        };

        // based on the info, assign a group... for demonstration we are assigning a random group
        State._deviceGroupId = new Random().Next(10, 12);

        var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
        return deviceGroup.RegisterDevice(State._deviceInfo);
    }
}
```

在组级别，按照我们的示例，我们的目标是监视组中的设备并聚合遥测数据以便为工程师生成警报。在此例中，我们的客户希望将工程师送到存在一定数量的故障设备的区域。当然，我们的客户希望通过最大程度减少花费在路上的工程时间来降低成本。为此，每个组参与者都保持每个区域的故障设备的聚合状态。当此数字达到阈值时，我们的客户会向该区域分派工程师，以更更换/修复这些设备。我们来了解一下其实现方式：

## IoT 代码示例 – 分组和聚合

```csharp
public interface IThingGroup : IActor
{
    Task RegisterDevice(ThingInfo deviceInfo);
    Task UnregisterDevice(ThingInfo deviceInfo);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingGroupState
{
    [DataMember]
    public List<ThingInfo> _devices;
    [DataMember]
    public Dictionary<string, int> _faultsPerRegion;
    [DataMember]
    public List<ThingInfo> _faultyDevices;
}

public class ThingGroup : Actor<ThingGroupState>, IThingGroup
{

    public override Task OnActivateAsync()
    {
        State._devices = new List<ThingInfo>();
        State._faultsPerRegion = new Dictionary<string, int>();
        State._faultyDevices = new List<ThingInfo>();

        return base.OnActivateAsync();
    }

    public Task RegisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Add(deviceInfo);
        return TaskDone.Done;
    }

    public Task UnregisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Remove(deviceInfo);
        return TaskDone.Done;
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        if (telemetry.DevelopedFault)
        {
            if (false == _faultsPerRegion.ContainsKey(telemetry.Region))
            {
                State._faultsPerRegion[telemetry.Region] = 0;
            }
            State._faultsPerRegion[telemetry.Region]++;
            State._faultyDevices.Add(_devices.Where(d => d.DeviceId == telemetry.DeviceId).FirstOrDefault());

            if (State._faultsPerRegion[telemetry.Region] > _devices.Count(d => d.Region == telemetry.Region) / 3)
            {
                Console.WriteLine("Sending an engineer to repair/replace devices in {0}", telemetry.Region);
                foreach(var device in State._faultyDevices.Where(d => d.Region == telemetry.Region).ToList())
                {
                    Console.WriteLine("\t{0}", device);
                }
            }
        }

        return TaskDone.Done;
    }
}
```

正如我们所见，它非常简单。在运行简单测试之后，输出如下所示：

```
Sending an engineer to repair/replace devices in Richmond
    Device = 33 Region = Richmond Version = 4
    Device = 79 Region = Richmond Version = 5
    Device = 89 Region = Richmond Version = 3
    Device = 63 Region = Richmond Version = 2
    Device = 85 Region = Richmond Version = 4
```

顺便说一下，你可能会认为设备按区域分组会更好。当然，在如何对设备进行分组/分区（是按地理位置、设备类型、版本还是租户等条件）方面，这完全取决于我们。此处有一点需要注意：设备状态与报告/分析。这就是我们明确模式图的原因。我们必须避免向参与者进行扇出查询以生成报告参与者；不必要的实例化和性能只是列举的两个缺点。我们建议使用两种方法进行报告：

* 使用组级别参与者（如聚合器）维护组的视图。让每个参与者只向此参与者主动推送相关数据。则此组级别参与者可以用于查看组中的设备的状态。

* 维护为进行报告而设计的显式存储。聚合器可以缓冲并定期将数据推送到报告存储，以便进一步查询和分析。

## 设备操作
到目前为止还不错，但在这些设备上进行的操作又如何呢？ 与对于设备一样，参与者可以公开操作接口，以便管理员可以在设备上执行操作。例如，管理员要基于季节性/区域变化将新配置推送到一组家用太阳能设备（是的，另一个实际方案）。

这里的主要想法是我们可以使用“Thing”参与者对每个设备进行精细控制，以及使用“ThingGroup”参与者对操作进行分组 — 无论它在聚合来自设备（如遥测）的数据还是在向大量设备发送数据（如配置）。平台负责分布设备参与者以及它们之间的消息传送，这对于开发人员完全透明。

当涉及到计算机到计算机 (M2M) 通信时，我们在分布式网络和图形部分中讨论的集散模式或是直接参与者到参与者交互会十分适用。对于 M2M 方案，我们可以为一组设备对“Directory/Index”参与者进行建模，以便允许它们发现并相互发送消息，如下所示：

![][2]

Azure Service Fabric Actors 还负责参与者的生存期。不妨这样考虑一下，我们会具有始终打开的设备，并且会具有偶尔连接的设备。为何我们要将负责每 14 小时连接一次的设备的参与者保留在内存中？ Azure Service Fabric 允许在需要的时间和位置保存和还原设备状态。

我们的结论是越来越多的客户会将 Azure Service Fabric 参与者视为其 IoT 实现的关键构建基块。

## 后续步骤
[模式：智能缓存](/documentation/articles/service-fabric-reliable-actors-pattern-smart-cache)

[模式：分布式网络和图形](/documentation/articles/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs)

[模式：资源调控](/documentation/articles/service-fabric-reliable-actors-pattern-resource-governance)

[模式：有状态服务组合](/documentation/articles/service-fabric-reliable-actors-pattern-stateful-service-composition)

[模式：分布式计算](/documentation/articles/service-fabric-reliable-actors-pattern-distributed-computation)

[若干反模式](/documentation/articles/service-fabric-reliable-actors-anti-patterns)

[Service Fabric Actor 简介](/documentation/articles/service-fabric-reliable-actors-introduction)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-2.png

<!---HONumber=74-->