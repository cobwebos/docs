---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: 7e7825e8247e78cbc0c0e9e22bdbd9326939e0a8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83998108"
---
下一步是将应用程序配置为使用帐户标识符和帐户密钥。 在[设置空间定位点资源](#create-a-spatial-anchors-resource)时，将它们复制到文本编辑器中。

在“项目”窗格中，导航到 `Assets\AzureSpatialAnchors.SDK\Resources`。 选择 `SpatialAnchorConfig`。 然后，在“检查器”窗格中，输入 `Account Key` 作为 `Spatial Anchors Account Key` 的值并输入 `Account ID` 作为 `Spatial Anchors Account Id` 的值。

接下来，打开 `SpatialAnchorManager.cs`。 查找 `CreateSessionAsync()` 和添加以下行，并替换之前的帐户域：`session.Configuration.AccountDomain = "MyAccountDomain";`。 可以直接在此注释 `// Configure authentication` 之前添加此行。
