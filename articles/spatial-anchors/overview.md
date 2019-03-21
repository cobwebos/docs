---
title: Azure 空间定位点概述 | Microsoft Docs
description: 了解 Azure 空间定位点如何帮助你开发跨平台的混合现实体验。
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 24a35b387a8b47d44f742303ddde0a0e8fb47fe6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57833792"
---
# <a name="azure-spatial-anchors-overview"></a>Azure 空间定位点概述

欢迎使用 Azure 空间定位点。 Azure 空间定位点为开发人员提供了构建空间感知混合现实应用程序的基本功能。 这些应用程序可能支持 Microsoft HoloLens、支持 ARKit 的基于 iOS 的设备以及支持 ARCore 的基于 Android 的设备。 通过 Azure 空间定位点，开发人员能够使用混合现实平台来感知空间，指定精确的兴趣点，并从支持的设备中重新调用这些兴趣点。
这些精确的兴趣点被称为空间定位点。

![跨平台](./media/cross-platform.png)

## <a name="examples"></a>示例

空间定位点启用的一些示例用例包括：

- [多用户体验](tutorials/tutorial-share-anchors-across-devices.md)。 空间定位点使同一地点的人们可以轻松参与多用户混合现实应用程序。 例如，两个人可以通过在桌子上放置虚拟棋盘来开始一场混合现实国际象棋游戏。 然后，通过在桌面上指向他们的设备，他们可以一起查看虚拟棋盘并与之交互。

- [方法查找](concepts/anchor-relationships-way-finding.md)。 开发人员还可以将空间定位点连接在一起，在它们之间创建关系。 例如，应用可以包括具有两个或更多个兴趣点的体验，用户必须与其交互以完成任务。 这些兴趣点可以通过连接的方式创建。 稍后，当用户完成多步骤任务时，应用可以请求当前定位点附近的定位点将用户指向任务中的下一步骤。

- [在现实世界中保留虚拟内容](concepts/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor)。 通过该应用，用户可在会议室墙上放置虚拟日历，人们可以使用手机应用或 HoloLens 设备查看该日历。 在工业环境中，用户可以通过将支持的设备照相机指向机器来接收关于机器的上下文信息。

Azure 空间定位点由受支持的设备平台的托管服务和客户端 SDK 组成。 以下部分提供有关使用 Azure 空间定位点开始构建应用的信息。

## <a name="next-steps"></a>后续步骤

使用空间定位点创建你的第一个应用。

> [!div class="nextstepaction"]
> [Unity](unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)
