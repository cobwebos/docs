---
title: Unity 游戏对象和组件
description: 介绍使用远程呈现实体和组件的 Unity 特定方法。
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 662c28196b06f5fbe49f69cb7145fdd33805e000
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89019039"
---
# <a name="interact-with-unity-game-objects-and-components"></a>与 Unity 游戏对象和组件交互

Azure 远程渲染 (ARR) 经过优化，可用于大量对象 (请参阅 [限制](../../reference/limits.md)) 。 尽管可以在主机上管理大型和复杂的层次结构，但在低功率设备上，在 Unity 中复制它们都是不可行的。

因此，当在主机上加载模型时，Azure 远程呈现会镜像客户端设备上有关模型结构的信息 (这将导致网络流量) ，但不会复制 Unity 中的对象和组件。 相反，它要求您手动请求所需的 Unity 游戏对象和组件，以便您可以将系统开销限制在实际所需的范围之外。 这样，就可以更好地控制客户端性能。

因此，Azure 远程呈现的 Unity 集成附带了附加功能，可根据需要复制远程呈现结构。

## <a name="load-a-model-in-unity"></a>在 Unity 中加载模型

在加载模型时，将获取对已加载模型的根对象的引用。 此引用不是 Unity 游戏对象，但您可以使用扩展方法将其转换为一个对象 `Entity.GetOrCreateGameObject()` 。 该函数需要类型为的参数 `UnityCreationMode` 。 如果传递 `CreateUnityComponents` ，则新创建的 Unity 游戏对象将另外用代理组件填充主机上存在的所有远程呈现组件。 但建议使用此选项，以 `DoNotCreateUnityComponents` 将开销降到最低。

### <a name="load-model-with-task"></a>加载包含任务的模型

```cs
LoadModelAsync _pendingLoadTask = null;
void LoadModelWithTask()
{
    _pendingLoadTask = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    _pendingLoadTask.Completed += (LoadModelAsync res) =>
    {
        // turn the root object into a Unity game object
        var gameObject = res.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        _pendingLoadTask = null;
    };

    // also listen to progress updates:
    _pendingLoadTask.ProgressUpdated += (float progress) =>
    {
        // progress is a fraction in [0..1] range
        int percentage = (int)(progress * 100.0f);
        // do something...
        // Since the updates are triggered by the main thread, we may access unity objects here.
    };
}
```

### <a name="load-model-with-unity-coroutines"></a>具有 Unity 协同程序的负载模型

```cs
IEnumerator LoadModelWithCoroutine()
{
    LoadModelAsync task = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    while (!task.IsCompleted)
    {
        int percentage = (int)(task.Progress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }

    task = null;
}
```

### <a name="load-model-with-await-pattern"></a>采用 await 模式的负载模型

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

上面的代码示例通过 SAS 使用模型加载路径，因为内置模型已加载。 使用 (的 blob 容器来寻址模型 `LoadModelAsync` ， `LoadModelParams`) 完全类似。

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

创建 Unity 游戏对象会将组件隐式添加 `RemoteEntitySyncObject` 到游戏对象。 此组件用于将实体转换同步到服务器。 默认情况下 `RemoteEntitySyncObject` ，要求用户显式调用 `SyncToRemote()` 以将本地 Unity 状态同步到服务器。 启用 `SyncEveryFrame` 将自动同步对象。

具有的对象 `RemoteEntitySyncObject` 可通过按钮在 Unity 编辑器中实例化并显示其远程子对象 **:::no-loc text="Show children":::** 。

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>包装器组件

附加到远程呈现实体的[组件](../../concepts/components.md)通过代理向 Unity 公开 `MonoBehavior` 。 这些代理表示 Unity 中的远程组件，并将所有修改转发到主机。

若要创建代理远程呈现组件，请使用扩展方法 `GetOrCreateArrComponent` ：

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>耦合生存期

远程 [实体](../../concepts/entities.md) 和 Unity 游戏对象的生存期是耦合的，而是通过绑定 `RemoteEntitySyncObject` 。 如果调用 `UnityEngine.Object.Destroy(...)` 此类游戏对象，也会删除远程实体。

若要销毁 Unity 游戏对象，而不影响远程实体，首先需要对调用 `Unbind()` `RemoteEntitySyncObject` 。

对于所有代理组件都是如此。 若要仅销毁客户端表示形式，需要 `Unbind()` 首先在代理组件上调用：

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>后续步骤

* [为 Unity 设置远程渲染](unity-setup.md)
* [教程：在 Unity 中操作远程实体](../../tutorials/unity/manipulate-models/manipulate-models.md)
