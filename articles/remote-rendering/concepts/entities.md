---
title: 实体
description: Azure 远程呈现 API 范围内的实体定义
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d7b9ecd048b080ae0ec9fd3fb7a4fb35009551b8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681943"
---
# <a name="entities"></a>实体

*实体*表示空间中的可移动对象，是远程呈现内容的基本构建基块。

## <a name="entity-properties"></a>实体属性

实体具有由位置、旋转和比例定义的变换。 实体本身没有任何可观察到的功能。 相反，行为通过附加到实体的组件添加。 例如，附加[CutPlane 组件](../overview/features/cut-planes.md)将在实体的位置创建切割平面。

实体本身最重要的方面是层次结构和生成的层次结构转换。 例如，当多个实体作为子实体附加到共享父实体时，所有这些实体可以通过更改父实体的转换以一致方式移动、旋转和缩放。

实体由其父实体唯一拥有，这意味着当父实体被`Entity.Destroy()`销毁时，其子级和所有连接[的组件](components.md)也是如此。 因此，通过调用`Destroy`返回的模型的根节点`AzureSession.Actions.LoadModelAsync()`或其 SAS 变体`AzureSession.Actions.LoadModelFromSASAsync()`，从场景中删除模型。

实体是在服务器加载内容或用户要向场景中添加对象时创建的。 例如，如果用户想要添加切割平面以可视化网格的内部，则用户可以创建平面应存在的实体，然后将切割平面组件添加到其中。

## <a name="query-functions"></a>查询函数

实体上有两种类型的查询函数：同步调用和异步调用。 同步查询只能用于客户端上存在且不需要太多计算的数据。 示例是查询组件、相对对象转换或父/子关系。 异步查询用于仅驻留在服务器上或涉及额外计算的数据，这些数据在客户端上运行成本太高。 示例包括空间边界查询或元数据查询。

### <a name="querying-components"></a>查询组件

要查找特定类型的组件，请使用`FindComponentOfType`：

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>查询转换

转换查询是对象的同步调用。 请务必注意，通过 API 查询的转换是相对于对象的父级的本地空间转换。 例外是根对象，对于根对象，本地空间和世界空间是相同的。

> [!NOTE]
> 没有专用 API 来查询任意对象的世界空间转换。

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

### <a name="querying-spatial-bounds"></a>查询空间边界

边界查询是使用一个实体作为根的对完整对象层次结构进行操作的异步调用。 请参阅有关[对象边界的](object-bounds.md)专用章节。

### <a name="querying-metadata"></a>查询元数据

元数据是存储在对象上的其他数据，服务器忽略这些数据。 对象元数据本质上是一组（名称、值）对，_其中值_可以是数字、布尔或字符串类型。 元数据可以随模型一起导出。

元数据查询是特定实体上的异步调用。 查询仅返回单个实体的元数据，而不是子图形的合并信息。

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

即使对象不包含任何元数据，查询也会成功。

## <a name="next-steps"></a>后续步骤

* [组件](components.md)
* [对象边界](object-bounds.md)
