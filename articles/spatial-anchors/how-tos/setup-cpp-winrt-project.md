---
title: 为 c + +/WinRT HoloLens 应用程序安装 Azure 空间锚
description: 将 c + +/WinRT HoloLens 项目配置为使用 Azure 空间锚。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 43d5c1ae03c359dcbef21f8e7ba3205bc6ab0004
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096107"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>在 c + +/WinRT HoloLens 项目中配置 Azure 空间锚

## <a name="requirements"></a>要求

* 随**通用 Windows 平台开发**工作负荷和**Windows 10 SDK (10.0.18362.0 或更新的) **组件一起安装了[Visual Studio 2019](https://www.visualstudio.com/downloads/) 。

## <a name="configuring-a-project"></a>配置项目

使用 [SpatialAnchors](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet 包分发用于 HoloLens 和 c + +/WinRT 的 Azure 空间锚。

按照 [此处](/nuget/consume-packages/install-use-packages-visual-studio) 的说明使用 Visual Studio 的 Nuget 包管理器将 [SpatialAnchors](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet 包安装到你的项目中。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何：在 c + + 中创建和定位定位点/WinRT](./create-locate-anchors-cpp-winrt.md)