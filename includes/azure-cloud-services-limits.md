---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 24e327a0b669df247d44e4bf8f05b693abb49990
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224377"
---
| Resource | 默认限制 | 最大限制 |
| --- | --- | --- |
| [每个部署的 Web 角色或辅助角色](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| 每个部署的[实例输入终结点](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) |25 |25 |
| 每个部署的[输入终结点](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) |25 |25 |
| 每个部署的[内部终结点](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) |25 |25 |
| 每个部署的[托管服务证书数](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |199 |

<sup>1</sup>每个具有 web 角色或辅助角色的 Azure 云服务可以有两个部署，一个用于生产，一个用于过渡。 此限制是指不同角色的数目，即，配置。 此限制不表示每个角色的实例数，即缩放。

