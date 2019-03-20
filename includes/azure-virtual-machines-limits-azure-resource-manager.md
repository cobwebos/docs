---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 9070aee55969c1cc0fdf3870a05a065aaa5a8bf3
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553322"
---
| 资源 | 默认限制 |
| --- | --- |
| 每个可用性集的虚拟机数 | 200 |
| 每个订阅的证书数 |不受限制<sup>1</sup> |

<sup>1</sup>使用 Azure 资源管理器时，证书将存储在 Azure 密钥保管库中。 证书的数目不受限制的订阅。 没有证书，每个部署，组成单个 VM 或可用性集的 1 MB 的限制。

