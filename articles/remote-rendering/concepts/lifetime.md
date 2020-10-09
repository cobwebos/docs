---
title: 对象和资源生存期
description: 介绍不同类型的生存期管理
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: d031ff4a6ee86da2843f0f18ac428c50f7cfc121
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80681865"
---
# <a name="object-and-resource-lifetime"></a>对象和资源生存期

Azure 远程呈现区分两种类型： **对象** 和 **资源**。

## <a name="object-lifetime"></a>对象生存期

*对象* 被视为用户可以自行创建、修改和销毁的对象。 对象可以自由复制，每个实例可以随时间而改变。 因此， [实体](entities.md) 和 [组件](components.md) 是对象。

对象的生存期完全受用户控制。 不过，它与客户端表示形式的生存期无关。 和等 `Entity` 类 `Component` 具有一个 `Destroy` 函数，必须调用该函数以释放远程呈现主机上的对象。 此外， `Entity.Destroy()` 将销毁该层次结构中的实体、其子级和所有组件。

## <a name="resource-lifetime"></a>资源生存期

*资源* 是其生存期由远程呈现主机完全管理的项。 资源在内部进行引用计数。 如果没有人多次引用它们，则将其释放。

大多数资源只能间接创建，通常是通过从文件中加载。 多次加载同一文件时，Azure 远程呈现将返回相同的引用，而不会再次加载数据。

许多资源是不可变的，例如 [网格](meshes.md) 和 [纹理](textures.md)。 但某些资源是可变的，例如 [材料](materials.md)。 由于资源通常是共享的，因此修改资源可能会影响多个对象。 例如，更改材料的颜色将更改使用网格的所有对象的颜色，而网格又引用该材料。

### <a name="built-in-resources"></a>内置资源

Azure 远程呈现包含某些内置资源，这些资源可在调用期间通过在其各自标识符前面追加来加载 `builtin://` `AzureSession.Actions.LoadXYZAsync()` 。 每个相关功能的文档中列出了可用的内置资源。 例如， [天空一章](../overview/features/sky.md) 列出了内置的天空纹理。

## <a name="general-lifetime"></a>常规生存期

所有对象和资源的生存期将绑定到连接。 断开连接后，所有内容都将被丢弃。 当重新连接到相同的会话时，场景图将为空，并清除所有资源。

在实践中，在断开连接后，将相同的资源加载到会话中通常比首次快。 这是因为大多数资源都必须在第一次从 Azure 存储下载，这不是第二次必要的，因此节省了相当长的时间。

## <a name="next-steps"></a>后续步骤

* [实体](entities.md)
* [组件](components.md)
* [模型](models.md)
