<properties
   pageTitle="Reliable Service 序列化 | Microsoft Azure"
   description="Service Fabric Reliable Service 序列化的概念文档"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson,tyadam"/>

<tags
   ms.service="service-fabric"
   ms.date="11/18/2015"
   wacn.date=""/>

# Reliable Services 中的序列化
可靠性状态管理器可以容纳多个可靠对象。
其中某些可靠对象可能是类似现成可靠字典和可靠队列的泛型数据结构。
由于它们是可靠的泛型数据结构，因此必须对其持有的泛型对象进行序列化，以便将其复制并保存到磁盘。

可靠性状态管理器支持三种类型的序列化程序：
* 自定义序列化程序、
* 有限类型的内置序列化程序、
* DataContractSerilizer

当可靠对象需要序列化对象时，它将向可靠性状态管理器查询指定类型的序列化程序。
可靠性状态管理器将首先检查是否已为输入类型注册了自定义序列化程序。
如果没有，它将检查是否存在可对该类型进行序列化的内置序列化程序。
可靠性状态管理器具有以下类型的内置序列化程序：guid、bool、byte、sbyte、char、decimal、double、float、int、uint、long、ulong、short、ushort 和 string。
如果没有，它将返回 DataContractSerializer。

本文重点介绍何时以及如何使用自定义序列化。

## 何时使用自定义序列化
使用自定义序列化有两个常见的原因：
* 性能
* 加密

对于内置类型未涵盖的类型，可以通过使用自定义生成的序列化程序取代 DataContractSerializer，以便实现显著的性能提升。
一项原因是自定义序列化程序不需要序列化类型信息。
Service Fabric 保证仅向给定类型的状态序列化程序提供该类型以进行序列化和反序列化。

序列化程序生成的序列化数据将按原样复制并保留在磁盘上。
对于保密的数据，可能需要确保已加密网络和磁盘上的位元。

## 如何使用自定义序列化
若要使用给定类型的自定义序列化程序，我们需要
* 生成自定义状态序列化程序
* 在 Reliable Service 中注册自定义状态序列化程序

### 如何实现自定义序列化程序
自定义序列化程序需要实现 IStateSerializer<T> 接口。
此接口中的两个核心为
* T Read(BinaryReader binaryReader)；
* void Write(T value, BinaryWriter binaryWriter)；

ReliableObject 使用第一个接口，从使用二进制编写器的流读取被序列化的对象。第二个用于执行反向操作：将对象写入使用二进制编写器的流。

下面是自定义类及其序列化程序的一个示例。

```C#
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }
    public short District { get; set; }
    public int Customer { get; set; }
    public long Order { get; set; }
```

```C#
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
    void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
    {
        writer.Write(value.Warehouse);
        writer.Write(value.District);
        writer.Write(value.Customer);
        writer.Write(value.Order);
    }

    OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
    {
        OrderKey value = new OrderKey();
        value.Warehouse = reader.ReadByte();
        value.District = reader.ReadInt16();
        value.Customer = reader.ReadInt32();
        value.Order = reader.ReadInt64();

        return value;
    }

    void IStateSerializer<OrderKey>.Write(OrderKey currentValue, OrderKey newValue, BinaryWriter writer)
    {
        ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
    }

    OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
    {
        return ((IStateSerializer<OrderKey>)this).Read(reader);
    }
}
```
>[AZURE.NOTE] 在上面的示例中，读取和写入重载的实现仅调用其对应重载。这是因为以下两种方法将用于目前尚不可用的功能中。

### 如何注册自定义序列化程序
若要注册自定义序列化程序，我们首先需要可以注册所有自定义序列化程序的方法。
此方法需要不采用参数并能够返回任务。
在此方法中，必须使用 IReliableStateManager.TryAddStateSerializer<T> 为 Reliable Service 注册所有自定义序列化程序。

```C#
protected Task InitializeStateSerializers()
{
    this.StateManager.TryAddStateSerializer(new OrderKeySerializer());
    return Task.FromResult(false);
}
```

下一步是将上述方法注册为一个委托，应当注册所有自定义状态序列化程序时，可靠性状态管理器将调用该委托。
由于从磁盘读取序列化的数据可能需要序列化程序，因此仅在启动本地恢复前启动 Reliable Service 副本时调用此方法。
注册序列化程序后，来自所有可靠对象的相关类型将使用此序列化程序对其对象进行序列化和反序列化。

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(
        new ReliableStateManagerConfiguration(
            onInitializeStateSerializersEvent : this.InitializeStateSerializers));
}
```
### 版本控制
Service Fabric 需要序列化程序能够向前和向后无限兼容。
对于使用内置序列化的类型，Service Fabric 确保向前和向后兼容性。
对于使用 DataContractSerializer 的类型或自定义序列化程序，用户需要永不执行重大更改。
有关 DataContract 版本控制，请参阅[数据协定版本管理](https://msdn.microsoft.com/zh-cn/library/ms731138.aspx)。
如果需要做出重大更改，需要将状态从具有旧数据版本的服务实例中移动到应用程序级别的具有新数据版本的服务中。

### 何时使用序列化程序
 * 可靠对象上的写入操作将序列化、复制该对象，并将其存储在日志中。
 * 记录足够的操作后，将对内存中的最新数据进行序列化并逐点检查到磁盘。
 * 若要重新生成副本，将从主副本逐字节（即不重新序列化数据）直接发送磁盘上经逐点检查的文件和日志中的最新数据。这些字节反序列化为辅助副本上的 C# 对象。
 * 在恢复期间，对检查点文件和最新数据日志进行反序列化。
 * 在备份期间，对检查点文件和最新数据日志进行逐字节复制。
 * 在还原期间，将以前备份的检查点文件和日志数据复制回原样待反序列化。

## 后续步骤
 * [Reliable Services 编程模型的高级用法](/documentation/articles/service-fabric-reliable-services-advanced-usage)

<!---HONumber=Mooncake_0321_2016-->