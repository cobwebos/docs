---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180035"
---
创建观察程序后，将为所请求的每个定位点触发 `AnchorLocated` 事件。 找到定位点或无法找到定位点时都会触发此事件。 如果发生这种情况，将在状态中说明原因。 在处理完观察程序的所有定位点（找到或未找到）后，将触发 `LocateAnchorsCompleted` 事件。 每个观察程序有 35 个标识符的限制。 
