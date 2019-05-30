---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 9070aee55969c1cc0fdf3870a05a065aaa5a8bf3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238338"
---
| Resource | 默认限制 |
| --- | --- |
| 每个可用性集的虚拟机数 | 200 |
| 每个订阅的证书数 |不受限制<sup>1</sup> |

<sup>1</sup>使用 Azure 资源管理器时，证书将存储在 Azure 密钥保管库中。 对于每个订阅，证书个数没有限制。 对于每个部署（包括单一 VM 或可用性集），证书的限制为 1-MB。

