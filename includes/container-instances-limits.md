---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 07/22/2020
ms.author: danlep
ms.openlocfilehash: 6878180ffedfaa53f25d2bdc6db72dcd7dd8b38b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87384815"
---
| 资源 | 限制 |
| --- | :--- |
| 每个区域每个订阅的标准 sku 容器组数 | 100<sup>1</sup> |
| 每个区域每个订阅的专用 sku 容器组数 | 0<sup>1</sup> |
| 每个容器组的容器数 | 60 |
| 每个容器组的卷数 | 20 |
| 每个订阅每个区域的标准 sku 核心数（CPU 数） | 10<sup>1,2</sup> | 
| 每个订阅每个区域的 K80 GPU 的标准 sku 核心数（CPU 数） | 18<sup>1,2</sup> |
| 每个订阅每个区域的 P100 或 V100 GPU 的标准 sku 核心数（CPU 数） | 0<sup>1,2</sup> |
| 每个 IP 的端口数 | 5 |
| 容器实例日志大小 - 正在运行的实例 | 4 MB |
| 容器实例日志大小 - 已停止的实例 | 16 KB 或 1,000 行 |
| 每小时创建容器次数 |300<sup>1</sup> |
| 每 5 分钟创建容器次数 | 100<sup>1</sup> |
| 每小时删除容器次数 | 300<sup>1</sup> |
| 每 5 分钟删除容器次数 | 100<sup>1</sup> |


<sup>1</sup>要请求提高上限，请创建一个 [Azure 支持请求][azure-support]。 包括 [Azure 免费帐户](https://azure.microsoft.com/offers/ms-azr-0044p/)和[面向学生的 Azure](https://azure.microsoft.com/offers/ms-azr-0170p/) 的免费订阅不符合提高上限或配额的条件。 如果有免费订阅，可将其[升级](../articles/cost-management-billing/manage/upgrade-azure-subscription.md)为即用即付订阅。<br />
<sup>2</sup>[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)订阅的默认上限。 其他类别类型的上限可能有所不同。<br/>

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
