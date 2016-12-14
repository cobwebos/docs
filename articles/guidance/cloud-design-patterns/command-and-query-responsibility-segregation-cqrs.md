---
title: "CQRS 模式 | Azure | Microsoft Docs"
description: "使用独立接口将读取数据的操作与更新数据的操作分离。"
categories:
- data-management
- design-implementation
- performance-scalability
keywords: "设计模式"
services: 
documentationcenter: na
author: dragon119
manager: bennage
tags: 
ms.service: guidance
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: mwasson
translationtype: Human Translation
ms.sourcegitcommit: c4963489ca53a90cab480116ec0a044501ccb5e9
ms.openlocfilehash: ececb5775b0b2d6f463c28e06e69468d601a952c

---

# <a name="command-and-query-responsibility-segregation-cqrs"></a>命令和查询责任分离 (CQRS)

使用独立接口将读取数据的操作与更新数据的操作分离。 这可以最大程度地提高性能、可伸缩性和安全性。 支持系统随着时间的推移而改进；防止更新命令在域级别引起合并冲突。

## <a name="context-and-problem"></a>上下文和问题

在传统的数据管理系统中，针对单个数据存储库中的相同实体集来执行命令（对数据的更新）和查询（数据请求）。 这些实体可以是关系数据库（如 SQL Server）中一个或多个表中的行的子集。

通常在这些系统中，所有创建、读取、更新和删除 (CRUD) 操作都应用于实体的相同表示法。 例如，通过数据访问层 (DAL) 从数据存储检索表示使用者的数据传输对象 (DTO)，并在屏幕上显示。 用户（可能是通过数据绑定）更新 DTO 的某些字段，然后 DAL 将 DTO 保存回数据存储中。 同一 DTO 可同时用于读取和写入操作。 该图说明了传统 CRUD 体系结构。

![传统 CRUD 体系结构](images/command-and-query-responsibility-segregation-cqrs-tradition-crud.png)

当仅将有限的业务逻辑应用于数据操作时，传统 CRUD 设计工作正常。 开发工具的基架机制可快速创建数据访问代码，然后可根据需要对其自定义。

但传统 CRUD 方法存在一些缺陷：

- 它通常意味着数据的读取和写入表示法之间不匹配，例如必须正确更新的附加列或属性（即使它们并非操作的一部分）。

- 当协作域内数据存储中的记录锁定时，它会面临数据争用的风险，其中多个执行组件会在相同的数据集上并行操作。 或者当使用乐观锁定时并发更新会引起更新冲突。 这些风险会随着系统复杂性和吞吐量的增加而增加。 此外，由于数据存储和数据访问层上的负载以及检索消息所需查询的复杂性，传统方法可能对性能具有负面影响。

- 它可以使管理安全性和权限更复杂，因为每个实体同时受读取和写入操作的影响，这可能会在错误的上下文中暴露数据。

> 若要深入了解有关 CRUD 方法的限制，请参阅 [CRUD, Only When You Can Afford It](https://msdn.microsoft.com/library/ms978509.aspx)（仅在可承受一定限制的情况下使用 CRUD）。 

## <a name="solution"></a>解决方案

命令和查询责任分离 (CQRS) 模式，它使用单独接口分离读取数据的操作（查询）和更新数据的操作（命令）。 这意味着，用于查询和更新的数据模型互不相同。 然后可以隔离模型，如下图所示（尽管这不是绝对要求）。

![基本 CQRS 体系结构](images/command-and-query-responsibility-segregation-cqrs-basic.png)


与基于 CRUD 的系统中使用的单个数据模型相比，在基于 CQRS 的系统中使用分离的数据查询和更新模型可简化设计和实现。 但缺点是 CQRS 代码不能通过基架机制自动生成（这与 CRUD 设计不同）。

用于读取数据的查询模型和用于写入数据的更新模型可访问相同的物理存储（可能通过使用 SQL 视图或通过生成动态投影）。 但它们均是通过将数据分隔到不同的物理存储来最大程度地提高性能、可伸缩性和安全性，如下图所示。

![具有单独读取和写入存储的 CQRS 体系结构](images/command-and-query-responsibility-segregation-cqrs-separate-stores.png)


读取存储可以是写入存储的只读副本，或者读取和写入存储可以具有完全不同的结构。 使用读取存储的多个只读副本可以极大地提高查询性能和应用程序 UI 响应能力，尤其是在只读副本所在的位置靠近应用程序实例的分布式方案中。 某些数据库系统 (SQL Server) 会提供其他功能（如故障转移的副本）以最大限度地提高可用性。

读取和写入存储的分离还允许彼此适当地缩放以匹配负载。 例如，读取存储通常会遇到高于写入存储的负载。

当查询/读取模型包含非规范化数据时（请参阅[具体化视图模式](materialized-view.md)），在读取应用程序中每个视图的数据时或在查询系统中的数据时，性能会实现最大化。 

## <a name="issues-and-considerations"></a>问题和注意事项

在决定如何实现此模式时，请考虑以下几点：

- 将数据划分到读取和写入操作的单独物理存储可提高系统性能和系统的安全性，但它也会增加复原和最终一致性方面的复杂性。 必须更新读取模型存储以反映写入模型存储更改，并且当用户发出基于陈旧读取数据的请求后将难以将其删除，这意味着无法完成该操作。

    > 有关最终一致性的说明，请参阅 [Data Consistency Primer](https://msdn.microsoft.com/library/dn589800.aspx)（数据一致性入门）。

- 请考虑将 CQRS 应用于系统中最能实现其价值的有限部分。 

- 部署最终一致性的典型方法是结合使用事件溯源和 CQRS，以便写入模型是由命令执行驱动的仅限附加事件流。 这些事件用于更新充当读取模型的具体化视图。 有关详细信息请参阅 [Event Sourcing and CQRS](https://msdn.microsoft.com/library/dn568103.aspx#EventSourcingandCQRS)（事件溯源和 CQRS）。

## <a name="when-to-use-this-pattern"></a>何时使用此模式

在以下情况下使用此模式： 

- 在其中对相同数据并行执行多个操作的协同域。 CQRS 允许用户定义具有足够粒度的命令，以最小化域级别的合并冲突（该命令可合并任何出现的冲突），即使在更新显示为相同类型的数据时也可执行此操作。

- 基于任务的用户界面，用户在该界面可按照一系列步骤组成的复杂过程指南或通过复杂域模型指南来操作。 此外，对已熟悉域驱动设计 (DDD) 技术的团队非常有用。 写入模型具有包含业务逻辑、输入验证和业务验证的完整命令处理堆栈，以确保写入模型中的每个聚合（将每个关联对象群集视作数据更改的一个单元）的所有内容始终一致。 读取模型不具有任何业务逻辑或验证堆栈，只返回 DTO 以在视图模型中使用。 读取模型最终与写入模型保持一致。 

- 应用场景：数据读取性能与数据写入性能必须分开进行优化（特别是当读取/写入比率非常高或需要水平缩放时）。 例如，在许多系统中读取操作数是写入操作数的数倍。 为了适应这种情况，请考虑向外扩展读取模型，但只在一个或几个实例上运行写入模型。 一小部分写入模型实例还有助于最大程度减少合并冲突。

- 应用场景：一个开发团队可专注于复杂域模型（作为写入模型一部分），而另一团队可专注于读取模型和用户界面。 

- 应用场景：系统会随着时间不断演变，并且可能会包含多个版本的模型，或业务规则会定期更改。

- 与其他系统集成时（尤其是与事件溯源集成时），一个子系统的临时故障错误不允许影响其他子系统的可用性。

在以下情况下不建议使用此模式：

- 域或业务规则简单。

- 简单 CRUD 样式的用户界面和相关数据访问操作充足。

- 跨整个系统的实现。 CQRS 可能对整体数据管理方案的某些特定组件非常有用，但在非必需时它会增加大量和不必要的复杂性。

## <a name="event-sourcing-and-cqrs"></a>事件溯源和 CQRS

CQRS 模式通常与事件溯源模式一起使用。 基于 CQRS 的系统使用分离的读取和写入数据模型，每个模型针对相关任务定制，并且通常位于物理分离存储中。 当使用[事件溯源](event-sourcing.md)模式时，事件存储是写入模型，并且是信息的官方源。 基于 CQRS 系统的读取模型提供数据的具体化视图，通常是高度非规范化视图。 针对应用程序的接口和显示要求定制这些视图，这有助于最大限度地提高显示和查询性能。

使用事件流作为写入存储（而不是使用某个时间点的实际数据），这可避免单个聚合上的更新冲突，并最大限度提高性能和可扩展性。 事件可以用于以异步方式生成用于填充读取存储的数据具体化视图。

由于事件存储是官方信息源，因此可删除具体化视图并重放所有过去事件，以便在系统升级时或必需更改读取模型时创建当前状态的新表示法。 具体化视图实际上是数据的持久只读缓存。

当结合使用 CQRS 和事件溯源模式时，请考虑以下方面：

- 在任何写入和读取存储分离的系统中，基于此模式的系统只会最终一致。 正在生成的事件与正在更新的数据存储之间的存在一定延迟。

- 本模式会增加复杂性，因为必需创建代码以启动和处理事件，组合或更新查询或读取模型所需的适当视图或对象。 结合事件溯源模式使用时，CQRS 模式的复杂性会使实现难以顺利完成，需要使用设计系统的其他方法。 但是，事件溯源可以更加轻松地对域创建模型，从而可以很方便地重新生成视图或创建新视图，因为它保留了想要执行的数据更改。

- 通过重放和处理特定实体或实体集合的事件来生成用于读取模型或数据投影的具体化视图可能需要大量的处理时间和资源。 特别是当如果需要长时间求和或分析值时，因为需要检查所有相关的事件。 通过以计划的时间间隔（例如已发生的特定操作的总计数或实体的当前状态）实现数据快照来解决此问题。

## <a name="example"></a>示例

以下代码显示了从 CQRS 实现（它对读取和写入模型使用不同的定义）的示例中提取的一些内容。 模型接口不规定基础数据存储的任何功能，而且它们可以不断变化并进行细微调整，因为这些接口是独立的。

下面的代码显示读取模型定义。

```csharp
// Query interface
namespace ReadModel
{
  public interface ProductsDao
  {
    ProductDisplay FindById(int productId);
    IEnumerable<ProductDisplay> FindByName(string name);
    IEnumerable<ProductInventory> FindOutOfStockProducts();
    IEnumerable<ProductDisplay> FindRelatedProducts(int productId);
  }

  public class ProductDisplay
  {
    public int ID { get; set; }
    public string Name { get; set; }
    public string Description { get; set; }
    public decimal UnitPrice { get; set; }
    public bool IsOutOfStock { get; set; }
    public double UserRating { get; set; }
  }

  public class ProductInventory
  {
    public int ID { get; set; }
    public string Name { get; set; }
    public int CurrentStock { get; set; }
  }
}
```

系统允许用户对产品制定费率。 应用程序代码使用以下代码中所示的 `RateProduct` 命令执行此操作。

```csharp
public interface Icommand
{
  Guid Id { get; }
}

public class RateProduct : Icommand
{
  public RateProduct()
  {
    this.Id = Guid.NewGuid();
  }
  public Guid Id { get; set; }
  public int ProductId { get; set; }
  public int rating { get; set; }
  public int UserId {get; set; }
}
```
系统使用 `ProductsCommandHandler` 类处理应用程序所发送的命令。 通常，客户端通过消息传递系统（如队列）将命令发送到域。 命令处理程序接受这些命令，并调用域接口方法。 每个命令的粒度旨在减少冲突请求。 下面的代码显示了 `ProductsCommandHandler` 类的概述。

```csharp
public class ProductsCommandHandler : 
    ICommandHandler<AddNewProduct>,
    ICommandHandler<RateProduct>,
    ICommandHandler<AddToInventory>,
    ICommandHandler<ConfirmItemShipped>,
    ICommandHandler<UpdateStockFromInventoryRecount>    
{
  private readonly IRepository<Product> repository;

  public ProductsCommandHandler (IRepository<Product> repository)
  {
    this.repository = repository;
  }

  void Handle (AddNewProduct command)
  {
    ...
  }

  void Handle (RateProduct command)
  {
    var product = repository.Find(command.ProductId);
    if (product != null)
    {
      product.RateProuct(command.UserId, command.rating);
      repository.Save(product);
    }
  }

  void Handle (AddToInventory command)
  {
    ...
  }

  void Handle (ConfirmItemsShipped command)
  {
    ...
  }

  void Handle (UpdateStockFromInventoryRecount command)
  {
    ...
  }
}
```

下面的代码显示了写入模型中的 `IProductsDomain` 接口。

```csharp
public interface IProductsDomain
{
  void AddNewProduct(int id, string name, string description, decimal price);
  void RateProduct(int userId int rating);
  void AddToInventory(int productId, int quantity);
  void ConfirmItemsShipped(int productId, int quantity);
  void UpdateStockFromInventoryRecount(int productId, int updatedQuantity);
}
```

另请注意 `IProductsDomain` 接口如何包含在域中有含义的方法。 通常情况下，在 CRUD 环境中这些方法将具有通用名称如 `Save` 或 `Update`，并且具有唯一参数 DTO。 CQRS 方法可以用于满足此组织业务和库存管理系统的需求。

## <a name="related-patterns-and-guidance"></a>相关模式和指南

实现此模式时，以下模式和指南将非常有用：

- [Data Consistency Primer](https://msdn.microsoft.com/library/dn589800.aspx)（数据一致性入门）。 描述通常在使用 CQRS 模式时由于读取和写入数据存储之间的最终一致性而引起的问题，以及如何解决这些问题。

- [Data Partitioning Guidance](https://msdn.microsoft.com/library/dn589795.aspx)（数据分区指南）。 描述如何将在 CQRS 模式中使用的读取和写入数据存储分成可独立管理和访问的多个分区，以提高可伸缩性、减少争用和优化性能。

- [事件溯源模式](event-sourcing.md)。 更加详细地介绍如何一起使用事件溯源和 CQRS 模式来简化复杂的域中任务的同时提高性能、可伸缩性和响应能力。 以及如何在维护可启用补偿操作的完整审核记录和历史记录的同时提供事务数据的一致性。

- [具体化视图模式](materialized-view.md)。 CQRS 实现的读取模型可包含写入模型数据的具体化视图，或该读取模型可用于生成具体化视图。

- 模式与实践指南 [CQRS Journey](http://aka.ms/cqrs)（CQRS 之旅）。 具体而言，[查询责任分离模式命令简介](https://msdn.microsoft.com/library/jj591573.aspx)探讨了模式及其适用性，[尾声：学到的经验教训](https://msdn.microsoft.com/library/jj591568.aspx)介绍使用此模式时会遇到的一些问题。

- [Martin Fowler 发布的 CQRS](http://martinfowler.com/bliki/CQRS.html) 文章介绍了该模式的基础以及获取其他有用资源的链接。

- [Greg Young 的文章](http://codebetter.com/gregyoung/)从多方面介绍了 CQRS 模式。



<!--HONumber=Nov16_HO3-->


