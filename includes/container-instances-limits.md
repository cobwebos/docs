---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: 33a82a55b03cde79d2d80826041ee6d43565476a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334818"
---
| 资源 | 限制 |
| --- | :--- |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)每个区域的标准 SKU 容器组 | 100<sup>1</sup> |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)每个区域的专用 SKU 容器组 | 0<sup>1</sup> |
| 每个容器组的容器数 | 60 |
| 每个容器组的卷数 | 20 |
| 每个 IP 的端口数 | 5 |
| 容器实例日志大小 - 正在运行的实例 | 4 MB |
| 容器实例日志大小 - 已停止的实例 | 16 KB 或 1,000 行 |
| 每小时创建容器次数 |300<sup>1</sup> |
| 每 5 分钟创建容器次数 | 100<sup>1</sup> |
| 每小时删除容器次数 | 300<sup>1</sup> |
| 每 5 分钟删除容器次数 | 100<sup>1</sup> |


<sup>1</sup>要请求提高上限，请创建一个 [Azure 支持请求][azure-support]。<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
