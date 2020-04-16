---
title: 更改环境和材料
description: 介绍如何在 Unity 场景中修改星空图和对象材料的教程。
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678678"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>教程：更改环境和材料

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 更改场景的环境图。
> * 修改材料参数。
> * 加载自定义纹理。

## <a name="prerequisites"></a>先决条件

本教程假设你已熟悉 [Tutorial:Working with remote entities in Unity](working-with-remote-entities.md)（教程：在 Unity 中使用远程实体）。 但是，你只需要一个可用于连接到会话并加载模型的 Unity 项目，具体请参阅 [Tutorial:Setting up a Unity project from scratch](project-setup.md)（教程：从头开始设置 Unity 项目）。

> [!TIP]
> [ARR 示例存储库](https://github.com/Azure/azure-remote-rendering)在 Unity 文件夹中包含了为所有教程准备的 Unity 项目，可将其用作参考。 

## <a name="change-the-environment-map"></a>更改环境图

Azure 远程渲染支持使用[星空盒](../../overview/features/sky.md)（有时也称为“环境图”）来模拟环境光。 当对象使用[基于物理学的渲染](../../overview/features/pbr-materials.md)时（我们的示例模型就是使用此技术），此功能尤其有用。  ARR 还随附了各种内置星空纹理，我们将在本教程中使用这些纹理。

创建名为 RemoteSky 的新脚本，并将其添加到新的 GameObject。  打开该脚本文件，并将其替换为以下代码：

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteSky : MonoBehaviour
{
    private int skyIndex = 0;

    private LoadTextureFromSASParams[] builtIns =
    {
        new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)
    };

    public async void ToggleSky()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        skyIndex = (skyIndex + 1) % builtIns.Length;

        var texture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(builtIns[skyIndex]).AsTask();

        Debug.Log($"Switching sky to: {builtIns[skyIndex].TextureId}");

        var settings = RemoteManagerUnity.CurrentSession.Actions.SkyReflectionSettings;

        settings.SkyReflectionTexture = texture;
    }

    private void OnGUI()
    {
        if (RemoteManagerUnity.IsConnected)
        {
            if (GUI.Button(new Rect(10, Screen.height - 50, 175, 30), "Toggle Sky"))
            {
                ToggleSky();
            }
        }
    }
}
```

请注意，上面之所以使用了 `LoadTextureFromSASAsync` 变体，是因为加载了内置纹理。 如果纹理是从[关联的 Blob 存储](../../how-tos/create-an-account.md#link-storage-accounts)加载的，请使用 `LoadTextureAsync` 变体。 在[模型加载部分](../../concepts/models.md#loading-models)可以找到演示如何对模型执行此操作的示例。

运行代码并切换星空图时，你会注意到模型中光线的急剧变化。 但是，背景会保持黑色，而你看不到实际的星空纹理。 这是有意而为的，因为使用增强现实设备渲染背景会分散注意力。 在适当的应用程序中，应使用类似于现实生活中周边环境的星空纹理，因为这会使对象显得更真实。

## <a name="modify-materials"></a>修改材料

在前一篇教程中，我们已使用[状态重写组件](../../overview/features/override-hierarchical-state.md)更改了选定对象的色调。 现在我们想要实现类似的效果，不过，我们的方法是修改对象的[材料](../../concepts/materials.md)。

首先，我们需要根据[第二篇教程](working-with-remote-entities.md)中所述，通过一个脚本来拾取对象。 如果你没有 RemoteRaycaster 脚本，现在请创建该脚本。  将其内容替换为以下代码：

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
}
```

将组件添加到 RemoteRendering 游戏对象。  该组件负责拾取鼠标光标下的对象，并将 RemoteModelEntity 组件添加到拾取的对象。  我们将在该组件类中实现实际的材料更改功能。

如果你没有 RemoteModelEntity 脚本，请创建该脚本并将其内容替换为以下代码： 

```csharp
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

// to prevent namespace conflicts
using ARRTexture = Microsoft.Azure.RemoteRendering.Texture;

public class RemoteModelEntity : MonoBehaviour
{
    private Color HighlightColor = new Color(1.0f, 0.4f, 0.1f, 1.0f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;
    private RemoteEntitySyncObject localSyncObject = null;
    private ARRMeshComponent meshComponent = null;
    private Color4 originalColor;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();

        meshComponent = GetComponent<ARRMeshComponent>();
        if (meshComponent == null)
        {
            var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
            if (mesh != null)
            {
                gameObject.BindArrComponent<ARRMeshComponent>(mesh);
                meshComponent = gameObject.GetComponent<ARRMeshComponent>();
            }
        }
        meshComponent.enabled = true;
    }

    public void SetFocus(bool on)
    {
        SetMaterialColor(on);
    }

    private void SetMaterialColor(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        // get the list of materials from the picked mesh
        // we only modify the first material; depending on how the model is authored, this may
        // work exactly as desired, or not ;-)
        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        // there are different types of materials, cast as necessary
        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
    }
}
```

在运行此代码时，鼠标悬停的对象会突出显示。 其效果与教程 2 中类似，但实现方式不同。 此处，我们将获取所拾取对象中的材料列表，然后将第一个材料修改为使用不同的 albedo 颜色。

> [!IMPORTANT]
> 请注意，此方法是否突出显示模型的适当部件取决于模型的创作方式。 如果每个对象只使用一个材料，则此方法可完美进行。 但是，如果模型的部件与材料不存在 1 对 1 的关系，则上述简单代码不会正常运行。

## <a name="use-a-different-texture"></a>使用不同纹理

[纹理](../../concepts/textures.md)通常是源模型的组成部分。 在[模型转换](../../quickstarts/convert-model.md)期间，所有纹理将转换为所需的运行时格式，并打包到最终的模型文件中。 若要在运行时替换某个纹理，需以 [DDS 文件格式](https://en.wikipedia.org/wiki/DirectDraw_Surface)将其保存，并将其上传到 Azure Blob 存储。 若要了解如何创建 Azure Blob 容器，请参阅[此快速入门指南](../../quickstarts/convert-model.md)。 有了 Blob 容器后，可以在 Azure 存储资源管理器中将其打开，并通过拖放操作上传文件。

在运行时端，可通过两种不同的方式对 Blob 存储中的纹理资产进行寻址：

* 按纹理的 SAS URI 对纹理寻址。 为此，请右键单击已上传的文件，然后从上下文菜单中选择“获取共享访问签名...”。  将此 SAS URI 与 `LoadTextureFromSASAsync` 函数变体一起使用（请参阅下面的示例代码）。
* 如果 [Blob 存储已关联到帐户](../../how-tos/create-an-account.md#link-storage-accounts)，则可直接按 Blob 存储参数对纹理进行寻址。 在本例中，相关的加载函数为 `LoadTextureAsync`。

现在，请打开 RemoteModelEntity 脚本，添加以下代码，然后删除重复的函数： 

```csharp
    private string textureFile = ""; //<SAS URI for your texture>
    private ARRTexture originalTexture = null;

    private async void SetMaterialTexture(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        ARRTexture newTexture = originalTexture;

        if (on)
        {
            var textureParams = new LoadTextureFromSASParams(textureFile, TextureType.Texture2D);

            newTexture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(textureParams).AsTask();
        }

        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
    }

    public void SetFocus(bool on)
    {
        if (string.IsNullOrEmpty(textureFile))
        {
            SetMaterialColor(on);
        }
        else
        {
            SetMaterialTexture(on);
        }
    }
```

运行此代码，并将鼠标悬停在模型上。 如果模型使用了正确的 UV 坐标，则纹理应该会显示出来， 否则只能看到颜色变化。

## <a name="next-steps"></a>后续步骤

有关如何在 Unity 中使用 Azure 远程渲染的简介教程系列到此结束。 接下来，请熟悉 ARR 的一些基本概念（例如[会话](../../concepts/sessions.md)、[实体](../../concepts/entities.md)和[模型](../../concepts/models.md)），加深理解。 此外，还可以深入探索其他各种功能，例如[灯光](../../overview/features/lights.md)、[外框渲染](../../overview/features/outlines.md)、[分层状态重写](../../overview/features/override-hierarchical-state.md)和[材料](../../concepts/materials.md)。

> [!div class="nextstepaction"]
> [Unity 游戏对象和组件](../../how-tos/unity/objects-components.md)
