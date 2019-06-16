---
title: 如何创建和定位在 Unity 中使用 Azure 空间的定位点的定位点 |Microsoft Docs
description: 深入了解如何创建和定位定位在 Unity 中使用 Azure 空间的定位点。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 5dc86aff7324b79f2c1b7a14b349337fc26a1901
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244317"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-unity"></a>如何创建和定位在 Unity 中使用 Azure 空间的定位点的定位点

> [!div  class="op_single_selector"]
> * [Unity](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [C++/NDK](create-locate-anchors-cpp-ndk.md)
> * [C++/WinRT](create-locate-anchors-cpp-winrt.md)

借助 Azure 空间定位点，可以在不同设备之间共享全球的定位点。 它支持多个不同的开发环境。 在本文中，我们将深入了解如何使用 Azure 空间的定位点 SDK，在 Unity 中，为：

- 正确设置和管理 Azure 空间的定位点会话。
- 创建并设置本地的定位点的属性。
- 将它们上载到云。
- 找到并删除云空间定位标记。

## <a name="prerequisites"></a>必备组件

若要完成本指南，请确保你有：

- 通读 [Azure 空间定位点概述](../overview.md)。
- 完成 [5 分钟快速入门](../index.yml)之一。
- 对 C# 和 Unity 有一个基本的了解。
- 对 <a href="https://developers.google.com/ar/discover/" target="_blank">ARCore</a>（若要使用 Android）或 <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a>（若要使用 iOS）有一个基本的了解。

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

详细了解如何[CloudSpatialAnchorSession](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession)类。

```csharp
    CloudSpatialAnchorSession cloudSession;
    // In your view handler
    this.cloudSession = new CloudSpatialAnchorSession();
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

详细了解如何[SessionConfiguration](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.sessionconfiguration)类。

```csharp
    this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```csharp
    this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

详细了解如何[TokenRequiredDelegate](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.tokenrequireddelegate)委托。

```csharp
    this.cloudSession.TokenRequired += (object sender, TokenRequiredEventArgs args) =>
    {
        args.AccessToken = @"MyAccessToken";
    };
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```csharp
    this.cloudSession.TokenRequired += async (object sender, TokenRequiredEventArgs args) =>
    {
        var deferral = args.GetDeferral();
        string myToken = await MyGetTokenAsync();
        if (myToken != null) args.AccessToken = myToken;
        deferral.Complete();
    };
```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```csharp
    this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```csharp
    this.cloudSession.TokenRequired += (object sender, TokenRequiredEventArgs args) =>
    {
        args.AuthenticationToken = @"MyAuthenticationToken";
    };
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```csharp
    this.cloudSession.TokenRequired += async (object sender, TokenRequiredEventArgs args) =>
    {
        var deferral = args.GetDeferral();
        string myToken = await MyGetTokenAsync();
        if (myToken != null) args.AuthenticationToken = myToken;
        deferral.Complete();
    };
```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-non-ios.md)]

详细了解如何[启动](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.start)方法。

```csharp
#if UNITY_IOS
    this.arkitSession = UnityARSessionNativeInterface.GetARSessionNativeInterface();
    this.cloudSession.Session = this.arkitSession.GetNativeSessionPtr();
#elif UNITY_ANDROID
    this.nativeSession = GoogleARCoreInternal.ARCoreAndroidLifecycleManager.Instance.NativeSession;
    this.cloudSession.Session = this.nativeSession.SessionHandle;
#elif UNITY_WSA || WINDOWS_UWP
    // No need to set a native session pointer for HoloLens.
#else
    throw new NotSupportedException("The platform is not supported.");
#endif

    this.cloudSession.Start();
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

详细了解如何[ProcessFrame](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.processframe)方法。

```csharp
#if UNITY_ANDROID
    long latestFrameTimeStamp = this.nativeSession.FrameApi.GetTimestamp();
    bool newFrameToProcess = latestFrameTimeStamp > this.lastFrameProcessedTimeStamp;
    if (newFrameToProcess)
    {
        this.cloudSession.ProcessFrame(this.nativeSession.FrameHandle);
        this.lastFrameProcessedTimeStamp = latestFrameTimeStamp;
    }
#endif
#if UNITY_IOS
    UnityARSessionNativeInterface.ARFrameUpdatedEvent += UnityARSessionNativeInterface_ARFrameUpdatedEvent;

    void UnityARSessionNativeInterface_ARFrameUpdatedEvent(UnityARCamera camera)
    {
        this.cloudSession.ProcessFrame(this.arkitSession.GetNativeFramePtr());
    }
#endif
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

详细了解如何[SessionUpdatedDelegate](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.sessionupdateddelegate)委托。

```csharp
    this.cloudSession.SessionUpdated += (object sender, SessionUpdatedEventArgs args)
    {
        var status = args.Status;
        if (status.UserFeedback == SessionUserFeedback.None) return;
        this.feedback = $"Feedback: {Enum.GetName(typeof(SessionUserFeedback), status.UserFeedback)} -" +
            $" Recommend Create={status.RecommendedForCreateProgress: 0.#%}";
    };
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

详细了解如何[CloudSpatialAnchor](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchor)类。

```csharp
    // Create a local anchor, perhaps by hit-testing and spawning an object within the scene
    Vector3 hitPosition = new Vector3();
#if UNITY_IOS
    var screenPosition = Camera.main.ScreenToViewportPoint(new Vector3(0.5f, 0.5f));
    ARPoint point = new ARPoint
    {
        x = screenPosition.x,
        y = screenPosition.y
    };
    var hitResults = UnityARSessionNativeInterface.GetARSessionNativeInterface().HitTest(point, ARHitTestResultType.ARHitTestResultTypeEstimatedHorizontalPlane | ARHitTestResultType.ARHitTestResultTypeExistingPlaneUsingExtent);
    if (hitResults.Count > 0)
    {
        // The hitTest method sorts the resulting list by increasing distance from the camera
        // The first hit result will usually be the most relevant when responding to user input
        ARHitTestResult hitResult = hitResults[0];
        hitPosition = UnityARMatrixOps.GetPosition(hitResult.worldTransform);
    }
#elif UNITY_ANDROID
    TrackableHit hit;
    TrackableHitFlags raycastFilter = TrackableHitFlags.PlaneWithinPolygon | TrackableHitFlags.FeaturePointWithSurfaceNormal;
    if (Frame.Raycast(0.5f, 0.5f, raycastFilter, out hit))
    {
        hitPosition = hit.Pose.position;
    }
#elif WINDOWS_UWP || UNITY_WSA
    RaycastHit hit;
    if (this.TryGazeHitTest(out hit))
    {
        hitPosition = hit.point;
    }
#endif

    Quaternion rotation = Quaternion.AngleAxis(0, Vector3.up);
    this.localAnchor = GameObject.Instantiate(/* some prefab */, hitPosition, rotation);
    this.localAnchor.AddARAnchor();

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    CloudSpatialAnchor cloudAnchor = new CloudSpatialAnchor();
    cloudAnchor.LocalAnchor = this.localAnchor.GetNativeAnchorPointer();
    await this.cloudSession.CreateAnchorAsync(cloudAnchor);
    this.feedback = $"Created a cloud anchor with ID={cloudAnchor.Identifier}");
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

详细了解如何[GetSessionStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.getsessionstatusasync)方法。

```csharp
    SessionStatus value = await this.cloudSession.GetSessionStatusAsync();
    if (value.RecommendedForCreateProgress < 1.0f) return;
    // Issue the creation request ...
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

详细了解如何[AppProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchor.appproperties)属性。

```csharp
    CloudSpatialAnchor cloudAnchor = new CloudSpatialAnchor() { LocalAnchor = localAnchor };
    cloudAnchor.AppProperties[@"model-type"] = @"frame";
    cloudAnchor.AppProperties[@"label"] = @"my latest picture";
    await this.cloudSession.CreateAnchorAsync(cloudAnchor);
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

详细了解如何[UpdateAnchorPropertiesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.updateanchorpropertiesasync)方法。

```csharp
    CloudSpatialAnchor anchor = /* locate your anchor */;
    anchor.AppProperties[@"last-user-access"] = @"just now";
    await this.cloudSession.UpdateAnchorPropertiesAsync(anchor);
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

详细了解如何[GetAnchorPropertiesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.getanchorpropertiesasync)方法。

```csharp
    var anchor = await cloudSession.GetAnchorPropertiesAsync(@"anchorId");
    if (anchor != nullptr)
    {
        anchor.AppProperties[@"last-user-access"] = @"just now";
        await this.cloudSession.UpdateAnchorPropertiesAsync(anchor);
    }
```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

详细了解如何[到期](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchor.expiration)属性。

```csharp
    cloudAnchor.Expiration = DateTimeOffset.Now.AddDays(7);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

详细了解如何[CreateWatcher](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.createwatcher)方法。

```csharp
    AnchorLocateCriteria criteria = new AnchorLocateCriteria();
    criteria.Identifiers = new string[] { @"id1", @"id2", @"id3" };
    this.cloudSession.CreateWatcher(criteria);
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

详细了解如何[AnchorLocatedDelegate](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.anchorlocateddelegate)委托。

```csharp
    this.cloudSession.AnchorLocated += (object sender, AnchorLocatedEventArgs args) =>
    {
        switch (args.Status)
        {
            case LocateAnchorStatus.Located:
                CloudSpatialAnchor foundAnchor = args.Anchor;
                // Go add your anchor to the scene...
                break;
            case LocateAnchorStatus.AlreadyTracked:
                // This anchor has already been reported and is being tracked
                break;
            case LocateAnchorStatus.NotLocatedAnchorDoesNotExist:
                // The anchor was deleted or never existed in the first place
                // Drop it, or show UI to ask user to anchor the content anew
                break;
            case LocateAnchorStatus.NotLocated:
                // The anchor hasn't been found given the location data
                // The user might in the wrong location, or maybe more data will help
                // Show UI to tell user to keep looking around
                break;
        }
    }
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

详细了解如何[DeleteAnchorAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.deleteanchorasync)方法。

```csharp
    await this.cloudSession..DeleteAnchorAsync(cloudAnchor);
    // Perform any processing you may want when delete finishes
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

详细了解如何[停止](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.stop)方法。

```csharp
    this.cloudSession.Stop();
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

详细了解如何[重置](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.reset)方法。

```csharp
    this.cloudSession.Reset();
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-unity.md)]

详细了解如何[Dispose](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.dispose)方法。

```csharp
    this.cloudSession.Dispose();
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]
