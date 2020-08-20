---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: 820353ffbb5e23e27c3039ad27a8f6507b9d2167
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608724"
---
<!--Create a media services asset REST-->

以下 Azure .NET 命令将创建新的媒体服务资产。 将值 `subscriptionID` 、和替换为 `resourceGroup` 当前使用 `amsAccountName` 的值。 通过在此处设置来为你的资产提供名称 `assetName` 。

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```
