---
ms.openlocfilehash: f478956c3a220ea4435c4f6e8b1096a1e9fdcadf
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014740"
---
创建观察程序后，将为所请求的每个定位点触发 `AnchorLocated` 事件。 找到定位点或无法找到定位点时都会触发此事件。 如果发生这种情况，将在状态中说明原因。 在处理完观察程序的所有定位点（找到或未找到）后，将触发 `LocateAnchorsCompleted` 事件。 每个观察程序有 35 个标识符的限制。 
