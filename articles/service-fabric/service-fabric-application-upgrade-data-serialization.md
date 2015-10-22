<properties
   pageTitle="Service Fabric 应用程序升级：数据序列化"
   description="用于确保成功升级应用程序的数据序列化的最佳做法。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
    ms.service="service-fabric"
    ms.date="08/05/2015"
    wacn.date=""/>


# Service Fabric 应用程序升级：数据序列化

在[应用程序滚动升级](documentation/articles/service-fabric-application-upgrade)过程中，升级应用于部分节点，一次一个升级域。在此过程中，一些升级域将位于较新版本的应用程序上，而一些升级域将位于较旧版本的应用程序上。此时，新版本的应用程序必须能够读取旧版本的数据，并且旧版本的应用程序必须能够读取新版本的数据。如果数据格式不向前和向后兼容，则升级可能失败，甚至可能丢失数据。本文介绍了你的数据格式的构成，以及确保数据向前和向后兼容的最佳做法。


## 数据格式由哪些部分构成？

在 Service Fabric 中，保留和复制的数据来自于你的 C# 类。对于使用 [Reliable Collections](documentation/articles/service-fabric-reliable-services-reliable-collections) 的应用程序，它是可靠词典和队列中的对象。对于使用[有状态 Reliable Actors](documentation/articles/service-fabric-reliable-actors-introduction) 应用程序，它是执行组件的备用状态。这些 C# 类必须可序列化，以进行保留和复制。因此，数据格式由已序列化的字段和属性及其序列化方式定义。例如，在 `IReliableDictionary<int, MyClass>` 中，数据是一个序列化的 `int` 和一个序列化的 `MyClass`。

### 数据格式更改

由于数据格式由 C# 类确定，所以对类进行更改可能会导致数据格式更改。必须格外小心，以确保滚动升级可以处理数据格式更改。可能会导致数据格式更改的示例：

- 添加或删除字段或属性
- 重命名字段或属性
- 更改字段或属性的类型
- 更改类名或命名空间

### 默认序列化程序

序列化程序通常负责读取数据并将其反其序列化到当前版本中，即使该数据位于较旧或*较新*版本中。默认序列化程序是[数据协定序列化程序](https://msdn.microsoft.com/library/ms733127.aspx)，它具有定义完善的版本控制规则。Reliable Collections 允许替代序列化程序，但 Reliable Actors 目前不允许。数据序列化程序在启用滚动升级中扮演着重要的角色。数据协定序列化程序是建议用于 Service Fabric 应用程序的序列化程序。


## 数据格式如何影响滚动升级

滚动升级期间，主要在两种情景下序列化程序可能会遇到较旧的数据版本或*较新*的数据版本：

1. 节点升级并启动备份后，新的序列化程序将会加载旧版本保留到磁盘的数据。
2. 滚动升级期间，群集中可能混合了新旧版本的代码。由于副本可能放置在不同的升级域中，序列化程序（其本身可能就是新版本或旧版本）可能会遇到新版本和旧版本的数据。

> [AZURE.NOTE]此处的“新版本”和“旧版本”是指正在运行的代码版本。“新序列化程序”是指在新版本的应用程序中执行的序列化程序代码。“新数据”是指新版本的应用程序中已序列化的 C# 类。

代码和数据格式的两个版本必须同时向前和向后兼容。如果它们不兼容，则滚动升级可能失败，甚至可能丢失数据。滚动升级可能失败，因为代码或序列化程序在遇到另一个版本时可能会引发异常或错误。例如，如果添加了一个新属性，但旧的序列化程序在反序列化期间弃用了该新属性，则数据可能会丢失。


## 数据协定

数据协定是为确保数据兼容所建议的解决方案。它具有定义完善的版本控制规则，可用于添加、删除和更改字段。它还支持处理未知字段、挂接到序列化和反序列化过程以及类继承。有关详细信息，请参阅[使用数据协定](https://msdn.microsoft.com/library/ms733127.aspx)。


## 后续步骤

[升级教程](documentation/articles/service-fabric-application-upgrade-tutorial)

[升级参数](documentation/articles/service-fabric-application-upgrade-parameters)

[高级主题](documentation/articles/service-fabric-application-upgrade-advanced)

<!---HONumber=74-->