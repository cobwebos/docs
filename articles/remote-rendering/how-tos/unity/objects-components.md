---
title: 统一游戏对象和组件
description: 描述用于远程渲染实体和组件的 Unity 特定方法。
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.openlocfilehash: 872f0e60943a674e5febf269dcb5b09fd08d77c9
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681150"
---
# <a name="interact-with-unity-game-objects-and-components"></a>与 Unity 游戏对象和组件交互

Azure 远程呈现 （ARR） 针对大量对象进行了优化（请参阅[限制](../../reference/limits.md)）。 虽然可以在主机上管理大型和复杂的层次结构，但在低功率设备上在 Unity 中复制它们是不可行的。

因此，在主机上加载模型时，Azure 远程呈现会镜像有关客户端设备上模型结构的信息（这将产生网络流量），但不会复制 Unity 中的对象和组件。 相反，它期望您手动请求所需的 Unity 游戏对象和组件，以便可以将开销限制为实际需要的内容。 这样，您可以对客户端性能进行更多控制。

因此，Azure 远程呈现的统一集成附带了可按需复制远程呈现结构的其他功能。

## <a name="load-a-model-in-unity"></a>在 Unity 中加载模型

加载模型时，您将获得对加载模型的根对象的引用。 此引用不是 Unity 游戏对象，但您可以使用扩展方法`Entity.GetOrCreateGameObject()`将其转换为一个。 该函数需要类型`UnityCreationMode`参数。 如果通过`CreateUnityComponents`，则新创建的 Unity 游戏对象将另外使用主机上存在的所有远程呈现组件的代理组件填充。 不过，建议您选择`DoNotCreateUnityComponents`，以尽量少用开销。

### <a name="load-model-with-task"></a>带任务的加载模型

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

### <a name="load-model-with-unity-coroutines"></a>具有 Unity 协同例程的加载模型

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

### <a name="load-model-with-await-pattern"></a>具有等待模式的加载模型

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

上述代码示例通过 SAS 使用模型加载路径，因为已加载内置模型。 通过 blob 容器（使用`LoadModelAsync`和`LoadModelParams`） 寻址模型的工作方式完全类似。

## <a name="remoteentitysyncobject"></a>远程实体同步对象

创建 Unity 游戏对象隐式`RemoteEntitySyncObject`地向游戏对象添加组件。 此组件用于将实体转换同步到服务器。 默认情况下`RemoteEntitySyncObject`，要求用户显式调用`SyncToRemote()`以将本地 Unity 状态同步到服务器。 启用`SyncEveryFrame`将自动同步对象。

具有 的`RemoteEntitySyncObject`对象的远程子级可以实例化，并通过 **"显示子级**"按钮在 Unity 编辑器中显示。

![远程实体同步对象](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>包装组件

附加到远程渲染实体的[组件](../../concepts/components.md)通过代理`MonoBehavior`s 向 Unity 公开。 这些代理表示 Unity 中的远程组件，并将所有修改转发到主机。

要创建代理远程呈现组件，请使用扩展方法`GetOrCreateArrComponent`：

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>耦合寿命

远程[实体](../../concepts/entities.md)和 Unity 游戏对象的生存期在通过 绑定时耦合`RemoteEntitySyncObject`。 如果使用此类游戏`UnityEngine.Object.Destroy(...)`对象进行调用，则远程实体也将被删除。

要销毁 Unity 游戏对象，在不影响远程实体的情况下，首先需要调用`Unbind()`。 `RemoteEntitySyncObject`

所有代理组件也是如此。 要仅销毁客户端表示形式，需要首先调用`Unbind()`代理组件：

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>后续步骤

* [为统一设置远程渲染](unity-setup.md)
* [教程：在 Unity 中使用远程实体](../../tutorials/unity/working-with-remote-entities.md)
