---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67172652"
---
与访问令牌一样，如果未设置 Azure AD 令牌，则必须处理 TokenRequired 事件，或在委托协议上实现 tokenRequired 方法。

可以通过设置事件参数上的属性来同步处理事件。
