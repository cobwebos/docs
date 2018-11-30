---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 54ca93cb61de993bed4dec4588e439955e628ea9
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279462"
---
| 资源 | 默认限制 |
| --- | --- |
| 每个可用性集的虚拟机数 | 200 |
| 每个订阅的证书数 |不受限制<sup>1</sup> |

<sup>1</sup>使用 Azure 资源管理器时，证书将存储在 Azure 密钥保管库中。 虽然订阅的证书数不受限制，但每个部署仍有 1 MB 的证书限制（包含单个 VM 或可用性集）。

