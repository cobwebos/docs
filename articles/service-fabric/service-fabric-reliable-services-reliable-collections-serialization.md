---
title: "Azure Service Fabric 中的 Reliable Collection 对象序列化 | Microsoft Docs"
description: "Azure Service Fabric Reliable Collections 对象序列化"
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: mcoskun
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: c14794b71ce7340d9e90a56d781c712e247ded06
ms.contentlocale: zh-cn
ms.lasthandoff: 05/23/2017

---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Azure Service Fabric 中的 Reliable Collection 对象序列化
Reliable Collections 通过复制和保留项目，确保这些项目在机器故障和电力中断时能够持久。
若要复制和保留项目，Reliable Collections 需要对其进行串行化。

Reliable Collections 从可靠状态管理器获取与给定类型对应的串行化程序。
可靠状态管理器包含内置序列化程序，允许针对给定类型注册自定义串行化程序。

## <a name="built-in-serializers"></a>内置串行化程序

可靠状态管理器包含针对一些常见类型的内置串行化程序，以便在默认情况下对其进行高效串行化。 对于其他类型，可靠状态管理器回退为使用 [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx)。
内置串行化程序更高效，因为它们知道其类型无法更改，且它们无需包含类型名称等有关类型的信息。

可靠状态管理器拥有针对以下类型的内置串行化程序： 
- Guid
- bool
- 字节
- sbyte
- byte[]
- char
- 字符串
- decimal
- double
- float
- int
- uint
- long
- ulong
- short
- ushort

## <a name="custom-serialization"></a>自定义序列化

自定义串行化程序通常用于提高性能，或用于在网络传输时以及在磁盘上加密数据。 自定义串行化程序通常比通用序列化程序更高效，因为它们不需要串行化有关类型的信息。 

[IReliableStateManager.TryAddStateSerializer<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer--1?Microsoft_ServiceFabric_Data_IReliableStateManager_TryAddStateSerializer__1_Microsoft_ServiceFabric_Data_IStateSerializer___0__) 用于为给定类型 T 注册自定义串行化程序。此注册应在 StatefulServiceBase 构造内发生，以确保在开始恢复前，所有 Reliable Collections 都有权访问相关串行化程序来读取其保留的数据。

```C#
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> 自定义串行化程序优先于内置串行化程序。 例如，如果为 int 注册了自定义串行化程序，则会使用它来串行化整数，而不使用 int 的内置串行化程序。

### <a name="how-to-implement-a-custom-serializer"></a>如何实现自定义串行化程序

自定义串行化程序需要实现 [IStateSerializer<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) 接口。

> [!NOTE]
> IStateSerializer<T> 包含读取和写入重载，可接受名为基值的附加 T。 此 API 用于差分序列化。 当前未公开差分序列化功能。 因此，在公开和启用差分序列化前，不会调用这两个重载。

以下是名为 OrderKey 的自定义类型示例，它包含四个属性

```C#
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

以下是 IStateSerializer<OrderKey> 的实现示例。
请注意，接受 baseValue 的读取和写入重载调用各自的重载来实现向前兼容。

```C#
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>可升级性
在[应用程序滚动升级](service-fabric-application-upgrade.md)过程中，升级应用于部分节点，一次一个升级域。 在此过程中，一些升级域将位于较新版本的应用程序上，而一些升级域将位于较旧版本的应用程序上。 在滚动更新期间，新版本的应用程序必须能够读取旧版本的数据，并且旧版本的应用程序必须能够读取新版本的数据。 如果数据格式不向前和向后兼容，则升级可能会失败（或更糟），甚至可能丢失数据。

如果使用内置串行化程序，无需担心兼容性问题。
但是，如果使用自定义串行化程序或 DataContractSerializer，数据必须能够无限向后和向前兼容。
换而言之，串行化程序的每个版本都需要能够序列化和反序列化类型的任何版本。

数据协定用户应遵循用于添加、删除和更改字段的定义完善的版本控制规则。 数据协定还支持处理未知字段、挂接到序列化和反序列化进程以及处理类继承。 有关详细信息，请参阅[使用数据协定](https://msdn.microsoft.com/library/ms733127.aspx)。

自定义串行化程序用户应遵循其使用的串行化程序的指导原则，以确保它向前和向后兼容。
支持所有版本的常用方法是在开头添加大小信息，并且仅添加可选属性。
这样一来，每个版本都可以读取尽可能多的数据并跳过数据流的其余部分。

## <a name="next-steps"></a>后续步骤
  * [序列化和升级](service-fabric-application-upgrade-data-serialization.md)
  * [Reliable Collections 的开发人员参考](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [使用 Visual Studio 升级应用程序](service-fabric-application-upgrade-tutorial.md)逐步讲解了如何使用 Visual Studio 进行应用程序升级。
  * [使用 Powershell 升级应用程序](service-fabric-application-upgrade-tutorial-powershell.md)逐步讲解了如何使用 PowerShell 进行应用程序升级。
  * 使用[升级参数](service-fabric-application-upgrade-parameters.md)来控制应用程序的升级方式。
  * 参考[高级主题](service-fabric-application-upgrade-advanced.md)，了解如何在升级应用程序时使用高级功能。
  * 参考[对应用程序升级进行故障排除](service-fabric-application-upgrade-troubleshooting.md)中的步骤来解决应用程序升级时的常见问题。

