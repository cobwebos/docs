---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: dcd2cda3bad2a13a83c5f3f6700e5a57471e2065
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653864"
---
<!--Create a media services asset REST-->

以下 Azure .NET 命令将创建新的媒体服务资产。 将值 `subscriptionID` 、和替换为 `resourceGroup` 当前使用 `amsAccountName` 的值。 通过在此处设置来为你的资产提供名称 `assetName` 。

[!code-csharp[Main](../../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]
