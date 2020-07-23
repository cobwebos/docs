---
ms.openlocfilehash: a673f02af9d92b7135e47961f13e0899a1f0e02f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "67172649"
---
如果未设置访问令牌，则必须处理 `TokenRequired` 事件，或在委托协议上实现 `tokenRequired` 方法。

可以通过设置事件参数上的属性来同步处理事件。
