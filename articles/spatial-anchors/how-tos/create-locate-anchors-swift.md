---
title: 如何创建和定位在 Swift 中使用 Azure 空间的定位点的定位点 |Microsoft Docs
description: 深入了解如何创建和定位定位在 Swift 中使用 Azure 空间的定位点。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 6147616156bc192ffd8d38ddcc636734d8d5179c
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915962"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-swift"></a>如何创建和定位在 Swift 中使用 Azure 空间的定位点的定位点

> [!div  class="op_single_selector"]
> * [Unity](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [C++/NDK](create-locate-anchors-cpp-ndk.md)
> * [C++/WinRT](create-locate-anchors-cpp-winrt.md)

借助 Azure 空间定位点，可以在不同设备之间共享全球的定位点。 它支持多个不同的开发环境。 在本文中，我们将深入了解如何使用 Azure 空间的定位点中的 SDK，Swift:

- 正确设置和管理 Azure 空间的定位点会话。
- 创建并设置本地的定位点的属性。
- 将它们上载到云。
- 找到并删除云空间定位标记。

## <a name="prerequisites"></a>必备组件

若要完成本指南，请确保你有：

- 通读[Azure 空间的定位点概述](../overview.md)。
- 完成之一[5 分钟快速入门](../index.yml)。
- Swift 的基本知识。
- 有关的基本知识<a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a>。

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

详细了解如何[ASACloudSpatialAnchorSession](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession)类。

```swift
    var _cloudSession : ASACloudSpatialAnchorSession? = nil
    // In your view handler
    _cloudSession = ASACloudSpatialAnchorSession()
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

详细了解如何[ASASessionConfiguration](https://docs.microsoft.com/objectivec/api/spatial-anchors/asasessionconfiguration)类。

```swift
    _cloudSession!.configuration.accountKey = "MyAccountKey"
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```swift
    _cloudSession!.configuration.accessToken = "MyAccessToken"
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

详细了解如何[tokenRequired](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#tokenrequired)协议方法。

```swift
    internal func tokenRequired(_ cloudSession:ASACloudSpatialAnchorSession!, _ args:ASATokenRequiredEventArgs!) {
        args.accessToken = "MyAccessToken"
    }
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```swift
    internal func tokenRequired(_ cloudSession:ASACloudSpatialAnchorSession!, _ args:ASATokenRequiredEventArgs!) {
        let deferral = args.getDeferral()
        myGetTokenAsync( withCompletionHandler: { (myToken: String?) in
            if (myToken != nil) {
                args.accessToken = myToken
            }
            deferral?.complete()
        })
    }

```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```swift
    _cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```swift
    internal func tokenRequired(_ cloudSession:ASACloudSpatialAnchorSession!, _ args:ASATokenRequiredEventArgs!) {
        args.authenticationToken = "MyAuthenticationToken"
    }
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```swift
    internal func tokenRequired(_ cloudSession:ASACloudSpatialAnchorSession!, _ args:ASATokenRequiredEventArgs!) {
        let deferral = args.getDeferral()
        myGetTokenAsync( withCompletionHandler: { (myToken: String?) in
            if (myToken != nil) {
                args.authenticationToken = myToken
            }
            deferral?.complete()
        })
    }

```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-ios.md)]

详细了解如何[启动](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#start)方法。

```swift
    _cloudSession!.session = self.sceneView.session;
    _cloudSession!.delegate = self;
    _cloudSession!.start()
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

详细了解如何[processFrame](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#processframe)方法。

```swift
    _cloudSession?.processFrame(self.sceneView.session.currentFrame)
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

详细了解如何[sessionUpdated](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#sessionupdated)协议方法。

```swift
    internal func sessionUpdated(_ cloudSession:ASACloudSpatialAnchorSession!, _ args:ASASessionUpdatedEventArgs!) {
        let status = args.status!
        if (status.userFeedback.isEmpty) {
            return
        }
        _feedback = "Feedback: \(FeedbackToString(userFeedback:status.userFeedback)) - Recommend Create=\(status.recommendedForCreateProgress * 100)"
    }
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

详细了解如何[ASACloudSpatialAnchor](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor)类。

```swift
    // Create a local anchor, perhaps by hit-testing and creating an ARAnchor
    var localAnchor : ARAnchor? = nil
    let hits = self.sceneView.session.currentFrame?.hitTest(CGPoint(x:0.5, y:0.5), types: ARHitTestResult.ResultType.estimatedHorizontalPlane)
    if (hits!.count == 0) return
    // The hitTest method sorts the resulting list by distance from the camera, increasing
    // The first hit result will usually be the most relevant when responding to user input
    localAnchor = ARAnchor(transform:hits![0].worldTransform)
    self.sceneView.session.add(anchor: _localAnchor!)

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    var cloudAnchor : ASACloudSpatialAnchor? = nil
    cloudAnchor = ASACloudSpatialAnchor()
    cloudAnchor!.localAnchor = localAnchor
    _cloudSession?.createAnchor(cloudAnchor!, withCompletionHandler: { (error: Error?) in
        if (error != nil) {
            _feedback = "Save Failed:\(error!.localizedDescription)"
            return
        }
        _feedback = "Created a cloud anchor with ID=\(cloudAnchor!.identifier!)"
    })
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

详细了解如何[getStatusWithCompletionHandler](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#getsessionstatus)方法。

```swift
    _cloudSession?.getStatusWithCompletionHandler( { (value:ASASessionStatus, error:Error?) in
        if (error != nil) {
            _feedback = "Session status error:\(error!.localizedDescription)"
            return
        }
        if (value!.recommendedForCreateProgress <> 1.0) {
            return
        }
        // Issue the creation request ...
    })
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

详细了解如何[appProperties](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor#appproperties)属性。

```swift
    var cloudAnchor : ASACloudSpatialAnchor? = nil
    cloudAnchor = ASACloudSpatialAnchor()
    cloudAnchor!.localAnchor = localAnchor
    cloudAnchor!.appProperties = [ "model-type" : "frame", "label" : "my latest picture" ]
    _cloudSession?.createAnchor(cloudAnchor!, withCompletionHandler: { (error: Error?) in
        // ...
    })
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

详细了解如何[updateAnchorProperties](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#updateanchorproperties)方法。

```swift
    var anchor : ASACloudSpatialAnchor? = /* locate your anchor */;
    anchor!.appProperties["last-user-access"] = "just now"
    _cloudSession?.updateAnchorProperties(anchor!, withCompletionHandler: { (error:Error?) in
        if (error != nil) {
            _feedback = "Updating Properties Failed:\(error!.localizedDescription)"
        }
    })
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

详细了解如何[getAnchorProperties](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#getanchorproperties)方法。

```swift
    _cloudSession?.getAnchorProperties("anchorId", withCompletionHandler: { (anchor:SCCCloudSpatialAnchor?, error:Error?) in
        if (error != nil) {
            _feedback = "Getting Properties Failed:\(error!.localizedDescription)"
        }
        if (anchor != nil) {
            anchor!.appProperties["last-user-access"] = "just now"
            _cloudSession?.updateAnchorProperties(anchor!, withCompletionHandler: { (error:Error?) in
                // ...
            })
        }
    })

```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

详细了解如何[到期](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor#expiration)属性。

```swift
    let secondsInAWeek = 60.0 * 60.0 * 24.0 * 7.0
    let oneWeekFromNow = Date(timeIntervalSinceNow: secondsInAWeek)
    cloudAnchor!.expiration = oneWeekFromNow
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

详细了解如何[createWatcher](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#createwatcher)方法。

```swift
    let criteria = ASAAnchorLocateCriteria()!
    criteria.identifiers = [ "id1", "id2", "id3" ]
    _cloudSession!.createWatcher(criteria)
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

详细了解如何[anchorLocated](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#anchorlocated)协议方法。

```swift
    internal func anchorLocated(_ cloudSession: ASACloudSpatialAnchorSession!, _ args: ASAAnchorLocatedEventArgs!) {
        let status = args.status
        switch (status) {
        case ASALocateAnchorStatus.located:
            let foundAnchor = args.anchor
            // Go add your anchor to the scene...
            break
        case ASALocateAnchorStatus.alreadyTracked:
            // This anchor has already been reported and is being tracked
            break
        case ASALocateAnchorStatus.notLocatedAnchorDoesNotExist:
            // The anchor was deleted or never existed in the first place
            // Drop it, or show UI to ask user to anchor the content anew
            break
        case ASALocateAnchorStatus.notLocated:
            // The anchor hasn't been found given the location data
            // The user might in the wrong location, or maybe more data will help
            // Show UI to tell user to keep looking around
            break
        }
    }
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

详细了解如何[删除](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#deleteanchor)方法。

```swift
    _cloudSession?.delete(cloudAnchor!, withCompletionHandler: { (error: Error?) in
        // Perform any processing you may want when delete finishes
    })
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

详细了解如何[停止](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#stop)方法。

```swift
    _cloudSession!.stop()
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

详细了解如何[重置](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#reset)方法。

```swift
    _cloudSession!.reset()
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-others.md)]

```swift
    _cloudSession = nil
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]
