---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: dd5c8878ce71b49b3a25f5d14a00bfe4f49ee769
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333938"
---
| 资源 | 默认限制 |
| --- | :--- |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的容器组数 | 100<sup>1</sup> |
| 每个容器组的容器数 | 60 |
| 每个容器组的卷数 | 20 |
| 每个 IP 的端口数 | 5 |
| 容器实例日志大小 - 正在运行的实例 | 4 MB |
| 容器实例日志大小 - 已停止的实例 | 16 KB 或 1000 行 |
| 每小时创建容器次数 |300<sup>1</sup> |
| 每 5 分钟创建容器次数 | 100<sup>1</sup> |
| 每小时删除容器次数 | 300<sup>1</sup> |
| 每 5 分钟删除容器次数 | 100<sup>1</sup> |


<sup>1</sup> 创建 [Azure 支持请求][azure-support]可请求提高上限。<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
