---
title: 实体
description: Azure 远程渲染 API 范围内实体的定义
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 1c49c7bfaa7714dda902d05537fbe3d8a55d5abe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613923"
---
# <a name="entities"></a>实体

实体表示空间中的可移动对象，是远程渲染内容的基本构造块。

## <a name="entity-properties"></a>实体属性

实体具有由位置、旋转和比例定义的转换。 实体本身没有任何可观测的功能。 而行为是通过附加到实体的组件添加的。 例如，附加 [CutPlaneComponent](../overview/features/cut-planes.md) 将在实体位置创建裁切平面。

实体本身的最重要方面是层次结构和产生的分层转换。 例如，将多个实体作为子级附加到共享的父实体时，可以通过更改父实体的转换，同时移动、旋转和缩放所有这些实体。 此外，实体的 `enabled` 状态可用于关闭层次结构中完整子关系图的可见性和对光线转换的响应。

实体由其父级唯一拥有，这意味着，当父级被 `Entity.Destroy()` 销毁时，其子级和所有连接的[组件](components.md)也会被销毁。 因此，从场景中删除模型（由 `AzureSession.Actions.LoadModelAsync()` 或其 SAS 变体 `AzureSession.Actions.LoadModelFromSASAsync()` 返回）可以通过在模型的根节点上调用 `Destroy` 来实现。

当服务器加载内容或用户想要将对象添加到场景中时，会创建实体。 例如，如果用户想要添加裁切平面来可视化网格内部，则用户可以创建应存在平面的实体，然后将该裁切平面组件添加到其中。

## <a name="create-an-entity"></a>创建实体

若要将新实体添加到场景中，例如，将其作为根对象传递以加载模型或向其附加组件，请使用以下代码：

```cs
Entity CreateNewEntity(AzureSession session)
{
    Entity entity = session.Actions.CreateEntity();
    entity.Position = new LocalPosition(1, 2, 3);
    return entity;
}
```

```cpp
ApiHandle<Entity> CreateNewEntity(ApiHandle<AzureSession> session)
{
    ApiHandle<Entity> entity(nullptr);
    if (auto entityRes = session->Actions()->CreateEntity())
    {
        entity = entityRes.value();
        entity->SetPosition(Double3{ 1, 2, 3 });
        return entity;
    }
    return entity;
}
```

## <a name="query-functions"></a>查询函数

实体上有两种类型的查询函数：同步调用和异步调用。 同步查询只能用于客户端上存在且不涉及大量计算的数据。 例如，查询组件、相对对象转换或父/子关系。 异步查询用于仅驻留在服务器上或涉及成本太高而无法在客户端上运行的额外计算的数据。 例如，空间边界查询或元数据查询。

### <a name="querying-components"></a>查询组件

若要查找特定类型的组件，请使用 `FindComponentOfType`：

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

```cpp
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType(ObjectType::CutPlaneComponent)->as<CutPlaneComponent>();

// or alternatively:
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>查询转换

转换查询是对对象的同步调用。 请务必注意，通过 API 查询的转换是相对于对象父级的本地空间转换。 例外情况是本地空间和世界空间相同的根对象。

> [!NOTE]
> 没有专用的 API 来查询任意对象的世界空间转换。

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

```cpp
// local space transform of the entity
Double3 translation = entity->GetPosition();
Quaternion rotation = entity->GetRotation();
```

### <a name="querying-spatial-bounds"></a>查询空间边界

边界查询是使用一个实体作为根实体在整个对象层次结构上操作的异步调用。 请参阅有关[对象边界](object-bounds.md)的专用章节。

### <a name="querying-metadata"></a>查询元数据

元数据是存储在对象中的其他数据，会被服务器忽略。 对象元数据实质上是一组（名称、值）对，其中值可以是数字、布尔值或字符串类型。 可以将元数据与模型一起导出。

元数据查询是对特定实体的异步调用。 查询仅返回单个实体的元数据，而不返回子图形的合并信息。

```cs
MetadataQueryAsync metaDataQuery = entity.QueryMetaDataAsync();
metaDataQuery.Completed += (MetadataQueryAsync query) =>
{
    if (query.IsRanToCompletion)
    {
        ObjectMetaData metaData = query.Result;
        ObjectMetaDataEntry entry = metaData.GetMetadataByName("MyInt64Value");
        System.Int64 intValue = entry.AsInt64;

        // ...
    }
};
```

```cpp
ApiHandle<MetadataQueryAsync> metaDataQuery = *entity->QueryMetaDataAsync();
metaDataQuery->Completed([](const ApiHandle<MetadataQueryAsync>& query)
    {
        if (query->GetIsRanToCompletion())
        {
            ApiHandle<ObjectMetaData> metaData = query->GetResult();
            ApiHandle<ObjectMetaDataEntry> entry = *metaData->GetMetadataByName("MyInt64Value");
            int64_t intValue = *entry->GetAsInt64();

            // ...
        }
    });
```

即使对象不包含任何元数据，查询也将成功。

## <a name="api-documentation"></a>API 文档

* [C # 实体类](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.entity)
* [C # RemoteManager CreateEntity ( # B1 ](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.createentity)
* [C + + 实体类](https://docs.microsoft.com/cpp/api/remote-rendering/entity)
* [C + + RemoteManager：： CreateEntity ( # B1 ](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#createentity)

## <a name="next-steps"></a>后续步骤

* [组件](components.md)
* [对象边界](object-bounds.md)
