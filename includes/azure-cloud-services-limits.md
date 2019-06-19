---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b24688b502a40e722d2fcc4436ff1824862f489
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66238439"
---
| Resource | 默认限制 | 最大限制 |
| --- | --- | --- |
| [每个部署的 web 或辅助角色](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [实例输入终结点](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint)每个部署 |25 |25 |
| [输入终结点](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint)每个部署 |25 |25 |
| [内部终结点](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint)每个部署 |25 |25 |
| 每个部署的[托管服务证书数](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |199 |

<sup>1</sup>每个 Azure 云服务中使用 web 或辅助角色可以有两个部署，一个用于生产，一个用于过渡。 此限制是指不同的角色，即配置数量。 此限制不是指每个角色，实例数，即缩放。

