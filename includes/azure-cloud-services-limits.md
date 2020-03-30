---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 3295c7e677e4c9bf7bff27614fe5fa4fa3200570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334693"
---
| 资源 | 限制 |
| --- | --- |
| [每个部署的 Web 或辅助角色](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| 每个部署的[实例输入终结点](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) |25 |
| 每个部署[的输入终结点](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) |25 |
| 每次部署[的内部终结点](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) |25 |
| 每个部署的[托管服务证书数](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |

<sup>1</sup>具有 Web 或辅助角色的每个 Azure 云服务可以有两个部署，一个用于生产，一个用于暂存。 此限制是指不同角色的数量，即配置。 此限制不是指每个角色的实例数，即缩放。

