---
title: 安装适用于 Unity 的 Azure 空间锚
description: 配置 Unity 项目以使用 Azure 空间锚。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/31/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: a23cff8bcef2d62b1e415997ffd6afc80cf47793
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812280"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>在 Unity 项目中配置 Azure 空间锚

## <a name="requirements"></a>要求

Azure 空间定位点当前支持具有以下配置的 Unity 2019.4 (LTS) 。

* Azure 空间锚 2.4.0 + 支持 Unity 2019.4 with AR Foundation 3.1。

## <a name="configuring-a-project"></a>配置项目

用于 Unity 的 Azure 空间定位点当前使用 Unity 资产包分发 (`.unitypackage`) ，可在[GitHub 版本](https://github.com/Azure/azure-spatial-anchors-samples/releases)中找到。

### <a name="import-the-asset-package"></a>导入资产包

1. 下载 `AzureSpatialAnchors.unitypackage` 适用于[GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases)版本的目标版本的文件。
2. 按照[此处](https://docs.unity3d.com/Manual/AssetPackagesImport.html)的说明将 Unity 资产包导入到项目中。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何：创建和定位 Unity 中的定位点](./create-locate-anchors-unity.md)
