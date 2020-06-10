---
title: 在 Unity 中使用远程实体
description: 介绍如何使用 ARR 实体的教程。
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: 5d995e9a5cdb6fc18532e0c3533959e9feece908
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021239"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>教程：在 Unity 中使用远程实体

[教程：从头开始设置 Unity 项目](project-setup.md)介绍了如何配置一个新的 Unity 项目来使用 Azure 远程渲染。 本教程将介绍每个 ARR 用户最常使用的功能。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 使用光线投射来拾取对象。
> * 重写对象状态，例如色调、选取状态和可见性。
> * 删除远程实体。
> * 四处移动远程实体。
> * 使用裁切平面查看对象的内部。

## <a name="prerequisites"></a>先决条件

* 本教程是在[教程：从头开始设置 Unity 项目](project-setup.md)的基础上编写的：

> [!TIP]
> [ARR 示例存储库](https://github.com/Azure/azure-remote-rendering)在 Unity 文件夹中包含了为所有教程准备的 Unity 项目，可将其用作参考。

## <a name="pick-objects"></a>拾取对象

我们想要与对象交互，因此，第一件事是拾取鼠标光标下的对象。

创建名为 RemoteRaycaster 的[新脚本](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html)，并将其整个内容替换为以下代码：

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using System.Threading.Tasks;
using UnityEngine;

[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRaycaster : MonoBehaviour
{
    public double MaxDistance = 30.0;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        arrService = GetComponent<ARRServiceUnity>();
    }

    private async Task<Entity> RemoteRayCast(Vector3 origin, Vector3 dir)
    {
        Entity entity = null;

        var raycast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), MaxDistance, HitCollectionPolicy.ClosestHit);

        var hits = await arrService.CurrentActiveSession.Actions.RayCastQueryAsync(raycast).AsTask();

        if (hits != null)
        {
            foreach (var hit in hits)
            {
                var hitEntity = hit.HitEntity;
                if (hitEntity == null)
                {
                    continue;
                }

                entity = hitEntity;
                break;
            }
        }

        return entity;
    }

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
        var ray = Camera.main.ScreenPointToRay(position);

        Raycast(ray.origin, ray.direction);
    }

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var entity = await RemoteRayCast(origin, direction);
        if (entity != null)
        {
            Debug.Log("Object Hit: " + entity.Name);
        }
    }
}
```

将此组件添加到场景中的 RemoteRendering 对象。

> [!WARNING]
>
> RemoteRaycaster 组件要求将 ARRServiceUnity 组件附加到同一个对象。  ARRServiceUnity 是一个帮助器类，使用它可以更轻松地访问某些 ARR 功能。 但是，场景中只能存在此组件的单个实例。 因此，请务必将需要 ARRServiceUnity 的所有组件添加到同一 GameObject。
> 若要从多个游戏对象访问 ARR 功能，请仅将 ARRServiceUnity 组件添加到其中的一个对象并在其他脚本中引用该对象，或者直接访问 ARR 功能。

按“播放”，连接到会话，然后加载模型。 现在请指向场景中的对象，并观察控制台输出。 控制台应会输出光标所在的每个部件的对象名称。

## <a name="highlight-objects"></a>突出显示对象

接下来，我们希望针对用户指向的模型部件提供视觉反馈。 为此，我们将一个 [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) 附加到拾取的实体。 此组件可用于在对象上启用或禁用各种功能。 此处，我们使用此组件来设置某种色调，并启用[边框渲染](../../overview/features/outlines.md)。

创建名为 RemoteModelEntity 的另一个脚本文件，并将其内容替换为以下代码：

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

public class RemoteModelEntity : MonoBehaviour
{
    public Color HighlightColor = new Color(1.0f, 0.0f, 0.0f, 0.1f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;

    private RemoteEntitySyncObject localSyncObject = null;
    private ARRHierarchicalStateOverrideComponent localStateOverride = null;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();
        localStateOverride = GetComponent<ARRHierarchicalStateOverrideComponent>();

        if (localStateOverride == null)
        {
            localStateOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
            var remoteStateOverride = localSyncObject.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

            if (remoteStateOverride == null)
            {
                // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                localStateOverride.Create(RemoteManagerUnity.CurrentSession);
            }
            else
            {
                // otherwise, bind our local stateOverride component to the remote component
                localStateOverride.Bind(remoteStateOverride);
            }
        }

        localStateOverride.RemoteComponent.TintColor = HighlightColor.toRemote();
    }

    public void OnDisable()
    {
        SetStateOverride(false);

        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.Enabled = false;
            localStateOverride.enabled = false;
        }
    }

    private void SetStateOverride(bool on)
    {
        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.UseTintColorState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.SelectedState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);
    }
}
```
> [!CAUTION]
> 不要将此脚本分配给任何 Unity 游戏对象，因为它将通过以下代码以编程方式进行分配。

接下来，我们必须扩展 RemoteRaycaster，以将 RemoteModelEntity 组件添加到刚刚拾取的对象。 

将以下代码添加到 RemoteRaycaster 实现，并删除重复的函数：

```csharp
    private RemoteModelEntity focusedModel = null;

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var remoteEntity = await RemoteRayCast(origin, direction);

        if (focusedModel != null)
        {
            if (focusedModel.Entity == remoteEntity)
            {
                // picked the same object as before
                return;
            }

            ClearFocus();
        }

        if (remoteEntity == null)
        {
            // picked no object at all
            return;
        }

        // get the instance of a Unity GameObject for the ARR entity
        var entityGO = remoteEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the game object has the RemoteModelEntity component
        focusedModel = entityGO.GetComponent<RemoteModelEntity>();
        if (focusedModel == null)
        {
            focusedModel = entityGO.AddComponent<RemoteModelEntity>();
        }

        focusedModel.SetFocus(true);
    }

    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        focusedModel = null;
    }
```

运行项目并指向某个模型，此时应会看到，该模型采用红色色调和白色选取边框。

## <a name="isolate-the-selected-object"></a>隔离选定对象

[HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) 的另一种用途是重写可见性。 这样，你便可以将选定对象与模型的其他部件隔离开来。 打开 RemoteModelEntity 脚本，添加以下代码，然后删除重复的函数：

```csharp
    private bool isolated = false;
    private HierarchicalStateOverrideComponent parentOverride = null;

    public void ToggleIsolate()
    {
        SetIsolated(!isolated);
    }

    public void SetIsolated(bool on)
    {
        if (localStateOverride == null || !localStateOverride.IsComponentValid || isolated == on)
        {
            return;
        }

        // find the top most parent object that has a HierarchicalStateOverrideComponent
        if (parentOverride == null)
        {
            var modelRoot = transform;

            while (modelRoot.parent != null)
            {
                modelRoot = modelRoot.parent;

                var parentSyncObject = modelRoot.GetComponent<RemoteEntitySyncObject>();

                var stateOverrideComp = parentSyncObject?.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                if (stateOverrideComp != null)
                {
                    parentOverride = stateOverrideComp;
                }
            }
        }

        if (parentOverride != null)
        {
            isolated = on;

            parentOverride.HiddenState = isolated ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.HiddenState = isolated ? HierarchicalEnableState.ForceOff : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);

        if (!on)
        {
            SetIsolated(false);
        }
    }
```

此代码依赖于在层次结构中的最顶层对象上使用状态重写组件，使所有对象均不可见。 然后，它会再次重写选定对象上的可见性，使该对象可见。 因此，我们需要在根对象上创建状态重写组件。

打开 RemoteRendering 脚本，将以下代码插入到 LoadModel 函数的最前面：

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

最后，需要通过某种方式来切换可见性。 打开 RemoteRaycaster 脚本并替换 Update 函数：

```csharp
    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        var ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Raycast(ray.origin, ray.direction);

        if (Input.GetMouseButtonDown(1) && focusedModel != null)
        {
            focusedModel.ToggleIsolate();
        }
    }
```

运行代码，并右键单击模型的某个部件。 模型的其他部件将会消失，只有显示突出的片段才保持可见。

## <a name="remove-gameobject-instances-of-remote-entities"></a>删除远程实体的 GameObject 实例

你可能已注意到，代码只会不断地创建对象，但永远不会清理对象。 在对象层次结构面板中也能看到这种情况。 如果在模拟期间展开远程对象层次结构，每次将鼠标悬停在模型的新部件上时，可以发现有越来越多的对象显示出来。

在场景中使用大量的对象会给性能造成负面影响。 始终应该清理不再需要的对象。

将以下代码插入 RemoteRaycaster 脚本，并删除重复的函数：

```csharp
    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        CleanHierarchy(focusedModel.gameObject);
        focusedModel = null;
    }

    private void CleanHierarchy(GameObject focusedGO)
    {
        var sync = focusedGO?.GetComponent<RemoteEntitySyncObject>();
        if (sync == null || !sync.IsEntityValid)
        {
            return;
        }

        sync.Entity.DestroyGameObject(EntityExtensions.DestroyGameObjectFlags.DestroyEmptyParents | EntityExtensions.DestroyGameObjectFlags.KeepRemoteRoot);
    }
```

## <a name="move-objects"></a>移动对象

接下来，我们想要四处移动选定对象。 在 RemoteRaycaster 脚本中插入以下代码，并删除重复的函数：

```csharp
    private Vector3 lastPosition = Vector3.zero;

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        if (Input.GetMouseButton(0))
        {
            if (focusedModel)
            {
                // note: mousePosition is in 2D screen-space coordinates and has no relation with
                // the 3D object position. This just happens to work good enough for demonstration.
                var delta = Input.mousePosition - lastPosition;
                focusedModel.transform.position += delta * Time.deltaTime;
            }
        }
        else
        {
            Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
            var ray = Camera.main.ScreenPointToRay(position);

            Raycast(ray.origin, ray.direction);

            if (Input.GetMouseButtonDown(1) && focusedModel != null)
            {
                focusedModel.ToggleIsolate();
            }
        }

        lastPosition = Input.mousePosition;
    }
```

> [!IMPORTANT]
> 如果运行此代码，将会发现没有任何情况发生。 这是因为，出于性能方面的原因，更改对象的变换不会自动将状态更改同步到服务器。 必须手动将此状态更改推送到服务器，或者在 RemoteEntitySyncObject 组件上启用 SyncEveryFrame。

打开 RemoteModelEntity 脚本并添加以下行：

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

再次运行该代码，应该就可以左键单击某个对象并将其四处拖动。

## <a name="add-a-cut-plane"></a>添加裁切平面

我们想要在本教程中尝试的最后一项功能是使用[裁切平面](../../overview/features/cut-planes.md)。 裁切平面会裁掉被渲染对象的部件，使你可以查看这些对象的内部。

在场景 CutPlane 中创建新的 GameObject。 创建一个新脚本并将其命名为 RemoteCutPlane。 将组件添加到新的 GameObject。

打开该脚本文件，并将其内容替换为以下代码：

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteCutPlane : MonoBehaviour
{
    private ARRCutPlaneComponent localCutPlaneComponent = null;
    private RemoteEntitySyncObject remoteEntitySync = null;

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            // can't do anything while we are not connected
            return;
        }

        if (localCutPlaneComponent == null)
        {
            localCutPlaneComponent = gameObject.CreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
        }

        if (remoteEntitySync == null)
        {
            remoteEntitySync = gameObject.GetComponent<RemoteEntitySyncObject>();
            remoteEntitySync.SyncEveryFrame = true;
        }

        localCutPlaneComponent.RemoteComponent.Normal = Axis.X;
        localCutPlaneComponent.RemoteComponent.FadeLength = 0.025f;
        localCutPlaneComponent.RemoteComponent.FadeColor = new Color4Ub(255, 128, 0, 255);
        localCutPlaneComponent.RemoteComponent.Enabled = true;
    }

    void OnDisable()
    {
        if (localCutPlaneComponent && localCutPlaneComponent.IsComponentValid)
        {
            localCutPlaneComponent.RemoteComponent.Enabled = false;
        }

        if (remoteEntitySync && remoteEntitySync.IsEntityValid)
        {
            remoteEntitySync.SyncEveryFrame = false;
        }
    }
}
```

现在，在运行代码时，应会看到模型如何被该平面切开。 可以选择 CutPlane 对象，并在“场景”窗口中移动和旋转它。  可以通过禁用裁切平面对象来打开和关闭裁切平面。

## <a name="next-steps"></a>后续步骤

现在，你已了解用来与远程对象进行交互的最重要功能。 在下一篇教程中，我们将了解如何自定义场景的外观。

> [!div class="nextstepaction"]
> [教程：更改环境和材料](changing-environment-and-materials.md)
