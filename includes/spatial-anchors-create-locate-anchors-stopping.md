---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67172671"
---
## <a name="pause-reset-or-stop-the-session"></a>暂停、重置或停止会话

若要暂时停止会话，可以调用 `Stop()`。 这样做会停止所有观察程序和环境处理，即使你调用 ProcessFrame()，也是如此。 然后，可以调用 `Start()` 以恢复处理。 恢复时，将保留已在会话中捕获的环境数据。
