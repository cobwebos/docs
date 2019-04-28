---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232331"
---
## <a name="pause-reset-or-stop-the-session"></a>暂停、 重置，或停止会话

若要停止会话暂时，你可以调用`Stop()`。 执行此操作将停止任何观察程序和环境处理，即使调用 ProcessFrame()。 然后可以调用`Start()`恢复处理。 当恢复时，维护该会话已捕获的环境数据。
