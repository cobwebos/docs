---
title: 照相机
description: 描述照相机设置和用例
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: 5ad39ad9f5e585029cff5d573a026702f259607e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90564793"
---
# <a name="camera"></a>照相机

若要确保本地和远程呈现的内容可以无缝地合成，则各种相机属性需要在服务器和客户端之间保持同步。 最值得注意的是相机转换和投影。 例如，本地呈现的内容必须使用呈现最新远程帧所用的同一相机转换和投影。

![本地和远程照相机](./media/camera.png)

在上图中，本地相机已移动，与服务器发送的远程帧相比。 因此，需要从与远程帧相同的角度呈现本地内容，最终会将生成的图像 reprojected 到本地相机空间中，这会在 [后期阶段 reprojection](late-stage-reprojection.md)中详细说明。

远程和本地呈现之间的延迟意味着对这些设置进行的任何更改都将应用到延迟。 因此，不能将任何新值立即用于相同的帧。

可在相机设置上设置近和远平面距离。 在 HoloLens 2 上，剩余的转换和投影数据由硬件定义。 使用 [桌面模拟](../../concepts/graphics-bindings.md)时，会通过其功能的输入来设置它们 `Update` 。

在第一个远程帧到达后，就可以在本地使用更改的值。 在 HoloLens 2 上，用于呈现的数据是由 *HolographicFrame* 提供的，就像在任何其他全息应用程序中一样。 在 [桌面模拟](../../concepts/graphics-bindings.md)上，通过函数的输出来检索它们 `Update` 。

## <a name="camera-settings"></a>照相机设置

可以在 "相机设置" 上更改以下属性：

**近和远平面：**

若要确保不能设置无效的范围， **NearPlane** 和 **FarPlane** 属性是只读的，并且存在单独的函数 **SetNearAndFarPlane** 来更改范围。 此数据将发送到帧末尾的服务器。

> [!IMPORTANT]
> 在 Unity 中，这是在更改靠近和远处的摄像机时自动处理的。

**EnableDepth**：

有时，出于调试目的禁用远程映像的深度缓冲区写入会很有帮助。 禁用深度还会使服务器停止发送深度数据，从而减少带宽。

> [!TIP]
> 在 Unity 中，提供了一个名为 **EnableDepthComponent** 的调试组件，可用于在编辑器 UI 中切换此功能。

可以按如下所示更改相机设置：

```cs
void ChangeCameraSetting(AzureSession session)
{
    CameraSettings settings = session.Actions.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<CameraSettings> settings = session->Actions()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
}
```

## <a name="api-documentation"></a>API 文档

* [C # CameraSettings](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C + + CameraSettings](https://docs.microsoft.com/cpp/api/remote-rendering/camerasettings)
* [C # GraphicsBindingSimD3d11 函数](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C + + GraphicsBindingSimD3d11：： Update 函数](https://docs.microsoft.com/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>后续步骤

* [图形绑定](../../concepts/graphics-bindings.md)
* [后期阶段重新投影](late-stage-reprojection.md)
