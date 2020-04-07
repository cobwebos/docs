---
title: 对象和资源生存期
description: 解释不同类型的生存期管理
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: d031ff4a6ee86da2843f0f18ac428c50f7cfc121
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681865"
---
# <a name="object-and-resource-lifetime"></a>对象和资源生存期

Azure 远程呈现区分了两种类型**objects**：对象**和资源**。

## <a name="object-lifetime"></a>对象生存期

*对象*被视为用户可以自行决定创建、修改和销毁的对象。 对象可以自由复制，每个实例可以随时间而发生变异。 因此[，实体](entities.md)和[组件](components.md)是对象。

对象的生存期完全由用户控制。 但是，它与客户端表示的生存期无关。 类喜欢`Entity`，`Component`并且具有`Destroy`必须调用的函数才能在远程呈现主机上解分配对象。 此外，`Entity.Destroy()`将销毁该层次结构中的实体、其子级和所有组件。

## <a name="resource-lifetime"></a>资源生存期

*资源*是其生存期完全由远程呈现主机管理的东西。 资源在内部计数引用。 当没人再引用他们时，他们会被交易。

大多数资源只能间接创建，通常是通过从文件加载这些资源。 多次加载同一文件时，Azure 远程呈现将返回同一引用，不再加载数据。

许多资源是不变的，例如[等杂](meshes.md)种和[纹理](textures.md)。 但是，某些资源是可变的，例如[材料](materials.md)。 由于资源通常是共享的，因此修改资源可能会影响多个对象。 例如，更改材质的颜色将更改使用等件的所有对象的颜色，而这些对象又引用该材质。

### <a name="built-in-resources"></a>内置资源

Azure 远程呈现包含一些内置资源，这些资源可以通过在调用`builtin://``AzureSession.Actions.LoadXYZAsync()`期间预先等待其各自的标识符来加载这些资源。 每个功能的文档中列出了可用的内置资源。 例如，[天空章节](../overview/features/sky.md)列出了内置的天空纹理。

## <a name="general-lifetime"></a>一般寿命

所有对象和资源的生存期绑定到连接。 断开连接时，一切都会被丢弃。 重新连接到同一会话时，场景图将为空，并且清除所有资源。

实际上，在断开连接后将同一资源加载到会话中的速度通常比第一次快。 这是因为大多数资源必须在第一次从 Azure 存储下载，这不需要第二次，从而节省了大量时间。

## <a name="next-steps"></a>后续步骤

* [实体](entities.md)
* [组件](components.md)
* [模型](models.md)
