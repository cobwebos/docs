---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694212"
---
## <a name="provide-feedback-to-the-user"></a>向用户提供反馈

可以编写代码来处理会话的已更新事件。 每次会话改进对你周围环境的理解时，都会触发此事件。 这样做使你可以：

- 使用 `UserFeedback` 类在设备移动时向用户提供反馈，而会话则会更新其环境理解。 为此，请按以下步骤操作：
- 确定在何时有足够的已跟踪空间数据来创建空间定位点。 可以通过 `ReadyForCreateProgress` 或 `RecommendedForCreateProgress` 来确定这一点。 一旦 `ReadyForCreateProgress` 超过 1，我们就会有足够的数据来保存云空间定位点，不过我们建议你等到 `RecommendedForCreateProgress` 大于 1 才这样做。
