---
title: 实体
description: Azure 远程呈现 API 范围内实体的定义
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d7b9ecd048b080ae0ec9fd3fb7a4fb35009551b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681943"
---
# <a name="entities"></a>实体

*实体*表示空间中的可移动对象，是远程呈现内容的基本构造块。

## <a name="entity-properties"></a>实体属性

实体具有由位置、旋转和缩放定义的转换。 本身的实体没有任何可观察的功能。 相反，行为是通过附加到实体的组件添加的。 例如，附加[CutPlaneComponent](../overview/features/cut-planes.md)将在实体位置创建一个切削平面。

实体本身的最重要方面是层次结构和生成的分层转换。 例如，将多个实体作为子级附加到共享的父实体时，可以通过更改父实体的转换，协同移动、旋转和缩放所有这些实体。

实体由其父级唯一拥有，这意味着，当父项被销毁时`Entity.Destroy()`，它的子级和所有连接的[组件](components.md)都是。 因此，从场景中删除模型是通过在模型的`Destroy`根节点上调用来完成的，该节点`AzureSession.Actions.LoadModelAsync()`由或其 SAS `AzureSession.Actions.LoadModelFromSASAsync()`变量返回。

当服务器加载内容或用户要将对象添加到场景中时，将创建实体。 例如，如果用户想要添加一个切削平面来可视化网格的内部，则用户可以创建一个该平面应存在的实体，然后将该平面组件添加到其中。

## <a name="query-functions"></a>查询函数

实体上有两种类型的查询函数：同步和异步调用。 同步查询仅可用于在客户端上存在且不涉及大量计算的数据。 例如，查询组件、相对对象转换或父/子关系。 异步查询用于仅驻留在服务器上的数据，或者涉及到在客户端上运行太昂贵的额外计算。 例如，空间边界查询或元数据查询。

### <a name="querying-components"></a>查询组件

若要查找特定类型的组件，请使用`FindComponentOfType`：

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>查询转换

转换查询是对对象的同步调用。 请务必注意，通过 API 查询的转换是相对于对象的父级的本地空间转换。 异常是指本地空间和世界空间相同的根对象。

> [!NOTE]
> 没有专用的 API 来查询任意对象的世界空间转换。

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

### <a name="querying-spatial-bounds"></a>查询空间边界

界限查询是异步调用，它们使用一个实体作为根在整个对象层次结构上操作。 请参阅有关[对象边界](object-bounds.md)的专用章节。

### <a name="querying-metadata"></a>查询元数据

元数据是存储在对象中的其他数据，会被服务器忽略。 对象元数据实质上是一组（名称、值）对，其中的值可以为数字、布尔_值_或字符串类型。 可以将元数据与模型一起导出。

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

即使对象不包含任何元数据，该查询也将成功。

## <a name="next-steps"></a>后续步骤

* [组件](components.md)
* [对象边界](object-bounds.md)
